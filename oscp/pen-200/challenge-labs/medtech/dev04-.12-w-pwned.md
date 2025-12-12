# DEV04 .12 \[W, Pwned]

## Domain Users

```
nxc rdp 172.16.187.0/24 -u yoshi -p 'Mushroom!' 
```

yoshi pwned it seems

`RDP 172.16.187.12 3389 DEV04 [+] medtech.com\yoshi:Mushroom! (Pwn3d!)`

Sort RDP connection next

```
xfreerdp /v:ip_address /u:username /p:password /cert:ignore +clipboard
xfreerdp /u:THM\phillip /p:Claire2008 /v:10.10.225.26 /dynamic-resolution
Username format : THM\philip [even tho in the login it shows <room name>\<username>, fix it]
/pth:[hash]
/cert-ignore (deprecated)
/drive:path/to/directory,share_name
https://commandmasters.com/commands/xfreerdp-linux/
+enforce-tls2, /cert-ignore, tls-seclevel

rdesktop -d [domain] -u [username] -p [password] [host]
WORKED
xfreerdp /d:MEDTECH /u:yoshi /p:'Mushroom!' /v:DEV04.medtech.com /dynamic-resolution /cert:ignore [/sec:nla]
```

I just dealt with Nla and credssp errors in lab. Banging my head against the wall and then accidently left off the -d domain name and it worked. It logged in as domain user and I'm not sure how but that was xfreerdp. I didn't try the fix with rdesktop. I just moved on

{% embed url="https://www.reddit.com/r/sysadmin/comments/8cwvp1/credsspnla_for_rdp_what_are_the_advantages/" %}

## Enumeration

```
Nmap scan report for 172.16.110.12
Host is up (0.0016s latency).
Not shown: 65521 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE       VERSION
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: MEDTECH
|   NetBIOS_Domain_Name: MEDTECH
|   NetBIOS_Computer_Name: DEV04
|   DNS_Domain_Name: medtech.com
|   DNS_Computer_Name: DEV04.medtech.com
|   DNS_Tree_Name: medtech.com
|   Product_Version: 10.0.20348
|_  System_Time: 2025-11-25T13:52:46+00:00
|_ssl-date: 2025-11-25T13:53:28+00:00; +1s from scanner time.
| ssl-cert: Subject: commonName=DEV04.medtech.com
| Issuer: commonName=DEV04.medtech.com
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-11-24T13:21:36
| Not valid after:  2026-05-26T13:21:36
| MD5:   c5af:19b5:ebb0:7bae:40bf:ebb0:9126:82b9
|_SHA-1: e76c:e031:290c:e956:64d7:fca3:e93c:44ed:29ee:cadf
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

```

### Ports

Port specific enumeration&#x20;

### Local Users

## Initial Foothold

yoshi has RDP access

## PrivEsc

see C:\TEMP\backup.exe

## Post Exploitation

WE GOT LEON CREDS

leon:rabbit:)

I got locked out trying to fuzz lmao

```
RDP         172.16.142.12   3389   DEV04            [-] medtech.com\leon:rabbit:) (STATUS_ACCOUNT_LOCKED_OUT)
```
