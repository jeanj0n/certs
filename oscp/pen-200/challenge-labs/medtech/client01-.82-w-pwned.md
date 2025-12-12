# CLIENT01 .82 \[W, Pwned]

## Domain Users

Yoshi is admin here

## Enumeration

IP range:

Computer name:

```

PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: MEDTECH
|   NetBIOS_Domain_Name: MEDTECH
|   NetBIOS_Computer_Name: CLIENT01
|   DNS_Domain_Name: medtech.com
|   DNS_Computer_Name: CLIENT01.medtech.com
|   DNS_Tree_Name: medtech.com
|   Product_Version: 10.0.22000
|_  System_Time: 2025-11-25T14:07:52+00:00
| ssl-cert: Subject: commonName=CLIENT01.medtech.com
| Issuer: commonName=CLIENT01.medtech.com
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-11-24T13:21:41
| Not valid after:  2026-05-26T13:21:41
| MD5:   0688:6323:9947:dfad:c65a:34be:4d35:56f0
|_SHA-1: 53fe:cc13:3301:ce9d:1170:1c9d:b2bc:c6df:7f8a:da23
|_ssl-date: 2025-11-25T14:08:31+00:00; +1s from scanner time.
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

```
SMB         172.16.110.83   445    CLIENT02         Share           Permissions     Remark
SMB         172.16.110.83   445    CLIENT02         -----           -----------     ------
SMB         172.16.110.83   445    CLIENT02         ADMIN$                          Remote Admin
SMB         172.16.110.83   445    CLIENT02         C               READ            
SMB         172.16.110.83   445    CLIENT02         C$                              Default share
SMB         172.16.110.83   445    CLIENT02         IPC$            READ            Remote IPC
SMB         172.16.110.83   445    CLIENT02         Windows         READ            
```

### Ports

Port specific enumeration&#x20;

### Local Users

Administrator:f26c0186c8ffcceb01fd2d7549e7ac1f

No Winrm so no real use

## Initial Foothold



## PrivEsc



## Post Exploitation

Found rabbit hole for user 'leon' at `C:\Users\Administrator.MEDTECH\Searches\hole.txt`

leon:rabbit!:) but it's a dummy
