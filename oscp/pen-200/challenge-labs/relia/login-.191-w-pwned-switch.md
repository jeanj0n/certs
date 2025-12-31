# LOGIN .191 \[W, Pwned SWITCH]

## Enumeration

```
Nmap scan report for 192.168.139.191
Host is up (0.12s latency).
Not shown: 995 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
| http-auth: 
| HTTP/1.1 401 Unauthorized\x0D
|_  Basic realm=192.168.139.191
|_http-server-header: Microsoft-IIS/10.0
|_http-title: 401 - Unauthorized: Access is denied due to invalid credentials.
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=login.relia.com
| Issuer: commonName=login.relia.com
| rdp-ntlm-info: 
|   Target_Name: RELIA
|   NetBIOS_Domain_Name: RELIA
|   NetBIOS_Computer_Name: LOGIN
|   DNS_Domain_Name: relia.com
|   DNS_Computer_Name: login.relia.com
|_ssl-date: 2025-12-12T05:29:46+00:00; 0s from scanner time.
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

### Files

None

### Ports

None

### Local Users

`dmzadmin` creds from WK01 can RDP here

## Initial Foothold

```
xfreerdp /u:dmzadmin /p:'SlimGodhoodMope' /v:192.168.202.191 /dynamic-resolution /cert:ignore
```

## PrivEsc

None

## Post Exploitation

Ligolo here ggs you're halfway there
