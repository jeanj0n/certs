# WINPREP .250 \[W, No Flags]

## Enumeration

```
Nmap scan report for 192.168.202.250
PORT      STATE SERVICE            VERSION
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
3389/tcp  open  ssl/ms-wbt-server?
| ssl-cert: Subject: commonName=WINPREP
| Issuer: commonName=WINPREP
|_ssl-date: 2025-12-12T05:01:59+00:00; 0s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: WINPREP
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-12-12T05:01:50
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
```

### Files



### Ports





### Local Users

## Initial Foothold



## PrivEsc



```
```

## Post Exploitation

