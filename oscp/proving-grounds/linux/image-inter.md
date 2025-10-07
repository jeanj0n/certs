# Image \[Inter]

## Report

ImageMagick executed on web server at port 80 is vulnerable to RCE. SUID is set for binary strace which can be used to spawn a new shell as root without dropping privs

## Cheatsheet

```
./strace -o /dev/null /bin/sh -p
```

## Loot

### Enumeration

192.168.57.178

ImageMagick 6.9.6-4 vulnerable to RCE

### Attack Vectors

CVE-2023-34152

### PrivEsc

`/usr/bin/strace` SUID and SGID set&#x20;

### Creds

local.txt:a36b67003a01311785f5d42775f96431

proof.txt:9c5fa1e7f2b39d754b6c1f95636eeca9

### Proof

None
