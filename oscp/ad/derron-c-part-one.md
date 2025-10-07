# Derron C Part One

Please organize this better w proper screenshots fuck this shitty mac istg

## Start

Every box in kali starts with the following

* enu
* exploits
* files
* loot

We havent messed with msfvenom payloads till now \[malicious exe execution]

exiftool works with exe files too \[not only for image files, metadata exists for anything]

### Initial Foothold

SeShutDown Privilege - why would shutting a system be so cool?

If you can reboot the system, you can also reboot/reload services on it right

we already have a few that we know are interesting look for some more maybe?

winPEAS enumerates local users \[see which ones are disabled/enabled]

We saw a domain user oscp/wyldside so maybe that'll come handy

42:04 - Unquoted service path with write access perms 'wisebootassistant'

```
shutdown -r -t 1 [time interval and reboot flags]
```

<div align="left"><figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption><p>how to clock domain user</p></figcaption></figure></div>

## MS01 -> MS02

`tree /f /a [folder_name]`

`dir /s/b *.log`

`dir /s/b *.txt`

<div align="left"><figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption><p>recursively list files</p></figcaption></figure></div>

<div align="left"><figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption><p>searching through hidden folders as well (appdata WAS NOT in above ss) - CAN WE GET THIS BY RUNNING WINPEAS AS ADMIN??</p></figcaption></figure></div>

nmap against domain controller after establishing ligolo (same as our notes good job)

`sudo nmap -T4 -p- -sCV --open -v dc01 -oN nmap.log`

\--open only performs scripts and version fingerprinting against open ports only&#x20;

Learning how to work with domain users and local users jeez

`smbclient -L //dc01 -U oscp/wildstyle`

`getuserSPN -request -dc-ip dc01 oscp.lab/wyldstyle`

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

the domain isn't just oscp, it's oscp.lab (syntax is everything)

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption><p>getting ready to password spray across MS02 and DC (MS01 just got in the way, find how to bypass that) currently he specifying a range</p></figcaption></figure>

Find user password combo in SMB share from IIS\_SVC access (SPN hash crack)

emmet Pwned MS02 -> direct admin access

## MS02 -> DC

find another user lord\_business on MS02, find out he is Domain Admin

secrets\_dump to extract hash to login as lord\_business on DC

NT hash wasn't extracted but cleartext creds were found (Learn to use mimikatz too incase yk)

Login as lord\_business to DC01 via evil-winrm and grab loot
