# Hutch \[Inter]

## Report

LDAP exposes creds for user 'fmcsorley' which can be used either to put a reverse shell via 'WebDAV' on port 80 and escalate as 'deafultapppool' or run bloodhound to see fmcsorley can read LAPS and evil-winrm as Administrator.

Learning domain controller naming structure was MEGA - bloodhound won't work without that so good job

## Enumeration

192.168.223.122

```
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
80/tcp    open  http          Microsoft IIS httpd 10.0
| http-webdav-scan: 
|   WebDAV type: Unknown
|   Allowed Methods: OPTIONS, TRACE, GET, HEAD, POST, COPY, PROPFIND, DELETE, MOVE, PROPPATCH, MKCOL, LOCK, UNLOCK
|   Server Type: Microsoft-IIS/10.0
|   Server Date: Sun, 19 Oct 2025 02:21:49 GMT
|_  Public Options: OPTIONS, TRACE, GET, HEAD, POST, PROPFIND, PROPPATCH, MKCOL, PUT, DELETE, COPY, MOVE, LOCK, UNLOCK
|_http-title: IIS Windows Server
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST COPY PROPFIND DELETE MOVE PROPPATCH MKCOL LOCK UNLOCK PUT
|_  Potentially risky methods: TRACE COPY PROPFIND DELETE MOVE PROPPATCH MKCOL LOCK UNLOCK PUT
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-10-19 02:21:01Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: hutch.offsec0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: hutch.offsec0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
Service Info: Host: HUTCHDC; OS: Windows; CPE: cpe:/o:microsoft:windows
```

#### SMB & RPCClient

Access Denied for guest

As fmcsorley

```
    Sharename       Type      Comment
    ---------       ----      -------
    ADMIN$          Disk      Remote Admin
    C$              Disk      Default share
    IPC$            IPC       Remote IPC
    NETLOGON        Disk      Logon server share 
    SYSVOL          Disk      Logon server share 
```

SYSVOL didn't get us anything - the top two were access denied

#### DNS

dig axfr hutch.offsec @192.168.223.122

Transfer failed

#### 80

IIS Default Page

#### Kerberos (88)

ASREP and Kerberoasting did not work

#### LDAP (userinfo?)

Worked - got creds for fmcsorley but password spraying didnt get us to another user

### Files

None

### Initial Foothold

LDAP exposes password for user in 'Description'

## PrivEsc

`bloodhound-python -c 'ALL' -u 'fmcsorley' -p 'CrabSharkJellyfish192' -d 'hutch.offsec' -dc 'hutchdc.hutch.offsec' -ns '192.168.223.122'`

LAPS readable by 'fmcsorley' via Bloodhound

## Loot

### Creds

`fmcsorley:CrabSharkJellyfish192` \[LDAP]

### Flags

2f323621b07aae423403fef35c109783

39bdabd38689aef9d8bbe167d4de5f2f

## Proof

Full name aka DC name woulde be last line of nmap + domain name ie. `hutchdc.hutch.offsec` \[bloodhound dont work without this]

{% embed url="https://github.com/notroj/cadaver" %}

{% embed url="https://www.linkedin.com/pulse/exploiting-webdav-gainrce-arav-budhiraja/" %}
