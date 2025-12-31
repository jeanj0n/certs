# LEGACY .249 \[W, Pwned]

## Enumeration

```
Nmap scan report for 192.168.202.249
PORT      STATE SERVICE       VERSION
80/tcp    open  http          Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: LEGACY
|_  System_Time: 2025-12-12T05:01:44+00:00
| ssl-cert: Subject: commonName=LEGACY
|_ssl-date: 2025-12-12T05:01:59+00:00; 0s from scanner time.
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
8000/tcp  open  http          Apache httpd 2.4.54 ((Win64) OpenSSL/1.1.1p PHP/7.4.30)
|_http-open-proxy: Proxy might be redirecting requests
|_http-server-header: Apache/2.4.54 (Win64) OpenSSL/1.1.1p PHP/7.4.30
| http-title: Welcome to XAMPP
|_Requested resource was http://192.168.139.249:8000/dashboard/
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

```

### Files

None

### Ports

#### 8000

Find phpinfo

Document root at&#x20;

```
C:/xampp/htdocs 
```

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

### Exploits

{% embed url="https://www.exploit-db.com/exploits/50616" %}

### Loot

#### Domain Users

None

#### Local Users

```
adrian
damon
```

#### Flags

local & proof done

## Initial Foothold

RiteCMS 3.1.0 under 'cms' directory vulnerable to auth RCE (default creds) get revshell as adrian

## PrivEsc

Find PowerShell history exposing creds for 'damon' who is admin OR SeImpersonate lol

```
PS C:\Users\adrian\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline> type ConsoleHost_history.txt
ipconfig
hostname
echo "Let's check if this script works running as damon and password i6yuT6tym@"
echo "Don't forget to clear history once done to remove the password!"
Enter-PSSession -ComputerName LEGACY -Credential $credshutdown /s

```

## Post Exploitation

mimikatz reveals nothing

This is also a standalone machine (See domain)
