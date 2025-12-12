# WEB01 .120 \[L, Pwned]

Each machine will have this

## Enumeration

IP: 192.168.110.120

Computer name:

```
Nmap scan report for 192.168.231.120
Host is up (0.060s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
| ssh-hostkey: 
|   3072 84:72:7e:4c:bb:ff:86:ae:b0:03:00:79:a1:c5:af:34 (RSA)
|   256 f1:31:e5:75:31:36:a2:59:f3:12:1b:58:b4:bb:dc:0f (ECDSA)
|_  256 5a:05:9c:fc:2f:7b:7e:0b:81:a6:20:48:5a:1d:82:7e (ED25519)
80/tcp open  http    WEBrick httpd 1.6.1 (Ruby 2.7.4 (2021-07-07))
|_http-server-header: WEBrick/1.6.1 (Ruby/2.7.4/2021-07-07)
| http-methods: 
|_  Supported Methods: GET HEAD OPTIONS
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

### Ports

Port specific enumeration&#x20;

### Local Users

SSH access

`offsec:century62hisan51`

## Initial Foothold



## PrivEsc

we can su to root

## Post Exploitation
