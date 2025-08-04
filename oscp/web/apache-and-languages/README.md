# Apache & Languages

## Apache

Find out which OS it's running on first from nmap scan

```
Root: /var/www
for multiple vhosts root is /var/www/[site1] and goes on
Vhost config: /etc/apache2/sites-enabled/000-default.conf
.htaccess and .htpasswd are at root of server
```

{% embed url="https://cwiki.apache.org/confluence/display/httpd/DistrosDefaultLayout#DistrosDefaultLayout-Apachehttpd2.4defaultlayout(apache.orgsourcepackage):" %}

## PHP

{% embed url="https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/File%20Inclusion/LFI-to-RCE.md" %}

{% embed url="https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/File%20Inclusion/Wrappers.md" %}

Try to use PoC exploit (phpinfo) first before attempting shell, this will also provide a lot of info

```
<?php phpinfo(); ?>
```

{% embed url="https://github.com/teambi0s/dfunc-bypasser" %}

To find disabled functions on PHP website

`python2 dfunc-bypasser.py --url [path to phpinfo page]`

Wrappers to look out for:

* filter (view source)
* phar (LFI2RCE)
* zip (LFI2RCE)

If there is an input field where it fetches a file and you control that parameter, you can view the source of php file itself or the apache log for LFI2RCE

{% embed url="https://sushant747.gitbooks.io/total-oscp-guide/content/local_file_inclusion.html" %}

PHP deserialization
