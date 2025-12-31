# EXTERNAL .248 \[W, Pwned]

## Enumeration

```
Nmap scan report for 192.168.139.248
Host is up (0.11s latency).
Not shown: 59933 closed tcp ports (reset), 5588 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE       VERSION
80/tcp    open  http          Microsoft IIS httpd 10.0
|_http-favicon: Unknown favicon MD5: 2DE6897008EB657D2EC770FE5B909439
| http-robots.txt: 16 disallowed entries (15 shown)
| /*/ctl/ /admin/ /App_Browsers/ /App_Code/ /App_Data/ 
| /App_GlobalResources/ /bin/ /Components/ /Config/ /contest/ /controls/ 
|_/Documentation/ /HttpModules/ /Install/ /Providers/
|_http-title: Home
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=EXTERNAL
|_ssl-date: 2025-12-12T04:55:20+00:00; 0s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: EXTERNAL
|_  System_Time: 2025-12-12T04:55:01+00:00
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49965/tcp open  ms-sql-s      Microsoft SQL Server 2019 15.00.2000.00; RTM
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Issuer: commonName=SSL_Self_Signed_Fallback

| ms-sql-ntlm-info: 
|   192.168.139.248:49965: 
|     Target_Name: EXTERNAL
|     NetBIOS_Domain_Name: EXTERNAL
|     NetBIOS_Computer_Name: EXTERNAL
|     DNS_Domain_Name: EXTERNAL
|     DNS_Computer_Name: EXTERNAL
|_    Product_Version: 10.0.20348
| ms-sql-info: 
|   192.168.139.248:49965: 
|     Version: 
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 49965
|_ssl-date: 2025-12-12T04:55:20+00:00; 0s from scanner time.
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

### Files

In Post Ex :D

### Ports

SMB

```
└─$ nxc smb 192.168.232.248 -u guest -p '' --shares
SMB         192.168.241.248 445    EXTERNAL         [*] Windows Server 2022 Build 20348 x64 (name:EXTERNAL) (domain:EXTERNAL) (signing:False) (SMBv1:False) 
SMB         192.168.241.248 445    EXTERNAL         [+] EXTERNAL\guest: 
SMB         192.168.241.248 445    EXTERNAL         [*] Enumerated shares
SMB         192.168.241.248 445    EXTERNAL         Share           Permissions     Remark
SMB         192.168.241.248 445    EXTERNAL         -----           -----------     ------
SMB         192.168.241.248 445    EXTERNAL         ADMIN$                          Remote Admin
SMB         192.168.241.248 445    EXTERNAL         C$                              Default share
SMB         192.168.241.248 445    EXTERNAL         IPC$            READ            Remote IPC
SMB         192.168.241.248 445    EXTERNAL         transfer        READ,WRITE      
SMB         192.168.241.248 445    EXTERNAL         Users           READ  
```

```
\DB-back (1)\New Folder
  .                                   D        0  Thu Oct 13 13:19:09 2022
  ..                                  D        0  Thu Oct 13 13:19:09 2022
  Emma                                D        0  Thu Oct 13 13:19:09 2022
  
  \DB-back (1)\New Folder\Emma\Documents
  .                                   D        0  Fri Oct 21 04:47:41 2022
  ..                                  D        0  Thu Oct 13 13:19:09 2022
  Database.kdbx                       A     2990  Fri Oct 21 04:47:41 2022
  
DNN webroot here
cd \r14_2022\build\DNN\wwwroot
```

### Local Users

## Initial Foothold

Put .aspx revshell in DNN root and access from public URL

## PrivEsc

```
SeImpersonate from 'defaultapppool'
```

## Post Exploitation

```
         [00000003] Primary
         * Username : Administrator
         * Domain   : EXTERNAL
         * NTLM     : 56e4633688c0fdd57c610faf9d7ab8df
```

### Keepass file

This is bait unfortunately, spray across all protocols to no avail

```
kpcli:/Database> show -f 2

Use of uninitialized value $comment in split at /usr/bin/kpcli line 6338.
Use of uninitialized value $val in pattern match (m//) at /usr/bin/kpcli line 3275.
Use of uninitialized value $val in sprintf at /usr/bin/kpcli line 3279.
Title: Sample Entry #2
Uname: Michael321
 Pass: 12345
  URL: https://keepass.info/help/kb/testform.html
Notes: 

kpcli:/Database> show -f 0

Title: RETIRED
Uname: bo
 Pass: Luigi=Papal1963
  URL: 
Notes: Backup Operator

kpcli:/Database/Network> show -f 0

Title: SQL
Uname: sa
 Pass: SAPassword_1998
  URL: 
Notes: Old password - depreciated

kpcli:/Database/Recycle Bin> show -f 0

Title: default
Uname: USE FOR NEW DB
 Pass: welcome1
  URL: 
Notes: 

kpcli:/Database/Windows> show -f 0

Title: emma
Uname: emma
 Pass: SomersetVinyl1!

kpcli:/Database/Windows> show -f 1

Title: Old
Uname: 
 Pass: HabitsAgesEnd123


bo:Luigi=Papal1963
Michael321:12345
```

<div align="left"><figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption><p>emma aint no domain user lol</p></figcaption></figure></div>
