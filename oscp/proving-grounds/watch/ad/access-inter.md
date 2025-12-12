# Access \[Inter]

## Report

{% embed url="https://medium.com/@siberfaqih/offsec-proving-grounds-access-a-complete-writeup-walkthrough-12ad7f6bad6f" %}
Use LDAP to query if SPN there in the first place
{% endembed %}

## Enumeration

192.168.247.187

```
PORT      STATE SERVICE       VERSION
53/tcp    open  domain?
80/tcp    open  http          Apache httpd 2.4.48 ((Win64) OpenSSL/1.1.1k PHP/8.0.7)
|_http-title: Access The Event
| http-methods: 
|   Supported Methods: GET POST OPTIONS HEAD TRACE
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.48 (Win64) OpenSSL/1.1.1k PHP/8.0.7
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-11-05 05:59:51Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: access.offsec0., Site: Default-First-Site-Name)
443/tcp   open  ssl/http      Apache httpd 2.4.48 ((Win64) OpenSSL/1.1.1k PHP/8.0.7)
|_http-title: Access The Event
|_http-server-header: Apache/2.4.48 (Win64) OpenSSL/1.1.1k PHP/8.0.7
|_ssl-date: TLS randomness does not represent time
| http-methods: 
|   Supported Methods: GET POST OPTIONS HEAD TRACE
| tls-alpn: 
|_  http/1.1
| ssl-cert: Subject: commonName=localhost
| Issuer: commonName=localhost
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: access.offsec0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        .NET Message Framing
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
```

SMB, RPC and LDAP bust

only HTTP(S) left

ticket.php is so sus

No valid users

```
ticket.php
forms
uploads
assets
```

File Upload

Upload .htaccess file with our extension to allow PHP exec and then our reverse shell

### Files

None

### Initial Foothold

Internal Kerboroasting

`.\Rubeus.exe kerberoast /outfile:kerberoast`

Crack hash and use `RunasCs.exe` to become `svc_mssql`

## PrivEsc

User svc\_msssql has SeManageVolume Privilege which has an exploit on GitHub that grants full access over the drive.

This lets us read the proof flag but not give an admin shell

Which is why we perform DLL injection to a common known Windows process (systeminfo) and target

The proof link below tells where to place the DLL and the command to run too (systeminfo)&#x20;

## Loot

### Creds

svc\_mssql:trustno1 (cracked SPN)

### Flags

Sort

## Proof

{% embed url="https://github.com/CsEnox/SeManageVolumeExploit/releases/tag/public?source=post_page-----12ad7f6bad6f---------------------------------------" %}

{% embed url="https://sirensecurity.io/blog/dllref/" %}
