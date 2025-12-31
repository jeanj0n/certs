# DEMO .246 \[L, Pwned]

## Enumeration

```
Nmap scan report for 192.168.139.246
PORT     STATE SERVICE  VERSION
80/tcp   open  http     Apache httpd 2.4.52 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Code Validation
|_http-server-header: Apache/2.4.52 (Ubuntu)
443/tcp  open  ssl/http Apache httpd 2.4.52 ((Ubuntu))
| ssl-cert: Subject: commonName=demo
| Subject Alternative Name: DNS:demo
| Issuer: commonName=demo
|_http-title: Code Validation
| tls-alpn: 
|_  http/1.1
| http-methods: 
|_  Supported Methods: GET HEAD POST
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_ssl-date: TLS randomness does not represent time
2222/tcp open  ssh      OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 42:2d:8d:48:ad:10:dd:ff:70:25:8b:46:2e:5c:ff:1d (ECDSA)
|_  256 aa:4a:c3:27:b1:19:30:d7:63:91:96:ae:63:3c:07:dc (ED25519)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

### Files

### Ports

SSH at 2222

Internal website at 8000

### Local Users

## Initial Foothold

Login using anita ssh key from web01

`ssh -i id_ecda anita@192.168.241.246 -p 2222`

`ss -lntu`

## PrivEsc

```
ss -tnlp
LISTEN                 0                      511                                        127.0.0.1:8000                                      0.0.0.0:* 
```

```
ssh -L 8000:127.0.0.1:8000 -i id_ecda anita@192.168.202.246 -p 2222
```

Upload revshell in /dev/shm and LFI at 'backend' endpoint directory traversal to get shell as  www-data who can sudo all

## Post Exploitation

None
