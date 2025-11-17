# Slort \[Inter]

## Report

HTTP website is vulnerable to RFI and get reverse shell.&#x20;

Find 'Backup' folder to see an exe 'TFTP' running every 5 minutes, check perms we can modify and confirm it's actually running. Add our malicious exe and reverse shell as SYSTEM

## Enumeration

192.168.202.53

```
PORT      STATE SERVICE       VERSION
21/tcp    open  ftp           FileZilla ftpd 0.9.41 beta
| ftp-syst: 
|_  SYST: UNIX emulated by FileZilla
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
3306/tcp  open  mysql?
| fingerprint-strings: 
|   HTTPOptions, NULL, SSLSessionReq: 
|_    Host '192.168.45.181' is not allowed to connect to this MariaDB server
4443/tcp  open  http          Apache httpd 2.4.43 ((Win64) OpenSSL/1.1.1g PHP/7.4.6)
|_http-server-header: Apache/2.4.43 (Win64) OpenSSL/1.1.1g PHP/7.4.6
| http-title: Welcome to XAMPP
|_Requested resource was http://192.168.202.53:4443/dashboard/
|_http-favicon: Unknown favicon MD5: 6EB4A43CB64C97F76562AF703893C8FD
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
5040/tcp  open  unknown
7680/tcp  open  pando-pub?
8080/tcp  open  http          Apache httpd 2.4.43 ((Win64) OpenSSL/1.1.1g PHP/7.4.6)
|_http-server-header: Apache/2.4.43 (Win64) OpenSSL/1.1.1g PHP/7.4.6
|_http-favicon: Unknown favicon MD5: 6EB4A43CB64C97F76562AF703893C8FD
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-open-proxy: Proxy might be redirecting requests
| http-title: Welcome to XAMPP
|_Requested resource was http://192.168.202.53:8080/dashboard/
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port3306-TCP:V=7.94%I=7%D=10/26%Time=68FE07A9%P=x86_64-pc-linux-gnu%r(N
SF:ULL,4D,"I\0\0\x01\xffj\x04Host\x20'192\.168\.45\.181'\x20is\x20not\x20a
SF:llowed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(HTTPOpti
SF:ons,4D,"I\0\0\x01\xffj\x04Host\x20'192\.168\.45\.181'\x20is\x20not\x20a
SF:llowed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(SSLSessi
SF:onReq,4D,"I\0\0\x01\xffj\x04Host\x20'192\.168\.45\.181'\x20is\x20not\x2
SF:0allowed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server");
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

```

<figure><img src="../../.gitbook/assets/image (129).png" alt=""><figcaption></figcaption></figure>

found user 'rupert' on computer 'SLORT' and web root

#### FTP

no guest access

#### SMB / RPC

Guest account disabled

#### HTTP (8080, 4443)

Same website on both, fuzz?

found endpoint 'site'&#x20;

#### SQL (3306)

Our host not allowed to connect

### Files

None

### Initial Foothold

Vulnerable parameter 'page' on index.php under site on both HTTP ports vulnerable to RFI

## PrivEsc

See a backup directory which runs an exe every 5 minutes.

<figure><img src="../../.gitbook/assets/image (130).png" alt=""><figcaption></figcaption></figure>

Confirm process is actually running

```
wmic process get Name,ProcessId,CreationDate

20251026045154.797813-480  cmd.exe                      2612       
20251026045154.844974-480  conhost.exe                  1396       
20251026045155.797741-480  cmd.exe                      3756       
20251026050000.563447-480  wsqmcons.exe                 1388       
20251026050001.018910-480  TFTP.EXE                     1120       
20251026050006.297745-480  WMIC.exe                     4920       
20251026050006.892505-480  cmd.exe                      3728       
```

## Loot

### Creds

None

### Flags

local:0ca5ce971207751eebf65eab78e8c188

proof:e3e5851ab9986c3690c159daf6738c98

## Proof
