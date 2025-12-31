# INTRANET .7 \[W, Pwned]

## Enumeration

```
Nmap scan report for 172.16.162.7
Host is up (0.056s latency).
Not shown: 65518 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE       VERSION
80/tcp    open  http          Apache httpd 2.4.53 ((Win64) OpenSSL/1.1.1n PHP/7.4.29)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-title: RELIA INTRANET &#8211; Just another WordPress site
|_Requested resource was http://172.16.162.7/wordpress/
|_http-generator: WordPress 6.0.3
|_http-server-header: Apache/2.4.53 (Win64) OpenSSL/1.1.1n PHP/7.4.29
|_http-favicon: Unknown favicon MD5: 6EB4A43CB64C97F76562AF703893C8FD
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
443/tcp   open  ssl/http      Apache httpd 2.4.53 ((Win64) OpenSSL/1.1.1n PHP/7.4.29)
| ssl-cert: Subject: commonName=localhost
| Issuer: commonName=localhost
| Public Key type: rsa
| Public Key bits: 1024
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2009-11-10T23:48:47
| Not valid after:  2019-11-08T23:48:47
| MD5:   a0a4:4cc9:9e84:b26f:9e63:9f9e:d229:dee0
|_SHA-1: b023:8c54:7a90:5bfa:119c:4e8b:acca:eacf:3649:1ff6
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-generator: WordPress 6.0.3
| http-title: RELIA INTRANET &#8211; Just another WordPress site
|_Requested resource was https://172.16.162.7/wordpress/
| tls-alpn: 
|_  http/1.1
|_ssl-date: TLS randomness does not represent time
|_http-favicon: Unknown favicon MD5: 6EB4A43CB64C97F76562AF703893C8FD
|_http-server-header: Apache/2.4.53 (Win64) OpenSSL/1.1.1n PHP/7.4.29
445/tcp   open  microsoft-ds?
3306/tcp  open  mysql         MariaDB (unauthorized)
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=INTRANET.relia.com
| Issuer: commonName=INTRANET.relia.com
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-12-19T05:03:02
| Not valid after:  2026-06-20T05:03:02
| MD5:   1cec:73e0:0c87:751e:fce2:2749:bf11:e1e0
|_SHA-1: 793b:8392:9076:9193:4875:01d1:8599:d5f2:9105:6ebe
| rdp-ntlm-info: 
|   Target_Name: RELIA
|   NetBIOS_Domain_Name: RELIA
|   NetBIOS_Computer_Name: INTRANET
|   DNS_Domain_Name: relia.com
|   DNS_Computer_Name: INTRANET.relia.com
|   DNS_Tree_Name: relia.com
|   Product_Version: 10.0.20348
|_  System_Time: 2025-12-20T09:20:51+00:00
|_ssl-date: 2025-12-20T09:21:32+00:00; 0s from scanner time.
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49670/tcp open  msrpc         Microsoft Windows RPC
49671/tcp open  msrpc         Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| nbstat: NetBIOS name: INTRANET, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:ab:87:2b (VMware)
| Names:
|   INTRANET<00>         Flags: <unique><active>
|   INTRANET<20>         Flags: <unique><active>
|_  RELIA<00>            Flags: <group><active>
| smb2-time: 
|   date: 2025-12-20T09:20:51
|_  start_date: N/A

```

### Files

### Ports

WordPress at 80

### Exploits

Simple PHP reverse shell

### Loot

## Initial Foothold

michelle can RDP here, see a WordPress website

```
MariaDB not allowed for root@localhost WUT
```

## PrivEsc

Drop a reverse shell at document root

```
Jenny is endgame
So is Dan and InternalAdmin
```

## Post Exploitation

Mimikatz

```
         * Username : andrea
         * Domain   : RELIA.COM
         * Password : PasswordPassword_6
```
