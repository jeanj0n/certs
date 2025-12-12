# MedTech

## Report

IP=192.168.45.167 \[ours]

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

## Chain Attack Flow

```
.121 [WEB02] - SQLi followed by SeImpersonate, mimikatz creds for joe which pwns .11 [FILES02]
.11 [FILES02] - joe is local admin and backup operator, find creds for wario with RDP for .83 
.83 [CLIENT02] - full perms to service running exe become SYSTEM, find 'yoshi' from WEB02 same creds as wario
.82 [CLIENT01] - yoshi is admin here [smb,winrm] find rabbithole text for leon
.12 [DEV04] - yoshi has admin rdp access, found by 'nxc rdp' find creds for leon who is domain admin
.10 [DC01] - domain controller, dump all creds using leon creds
.120 [WEB01] - login using 'offsec' creds from DC, can run anything as root
.13 [PROD01] - use leon creds
.122 [VPN] - bruteforce creds for user 'offsec' and fetch ssh key for 'mario' after privesc
.14 - login via ssh key of mario
```

## Flags

```
DONE
```

## Proof

```
.120 - proof only
.121 - proof only
.122 - local and proof
.10 - proof only
.11 - local and proof 
.12 - local and proof 
.13 - proof only
.14 - local only 
.82 - proof only
.83 - local and proof 
```

Have you checked **Joe**'s documents? (big up ssSss & OffsecMentor-t3z)

## Learn

DO THIS

* Polyglot shell, .odt file, .htaccess
* Find the computer name before looking at discord lol, know what to look for

WE GOT THESE (25/11)

* Post exploitation w mimkatz and winrm shell as admin for easier persistence (or new admin user)
* Ligolo plus powershell Invoke Portscan incase ligolo slow or fail
* Spraying creds (SMB gon be there for most Windows systems, winrm if you lucky)
* Turn off firewall after machine compromise (listener port problems) - REAL RESOURCE
* **Cred hunting after compromise, Program Files and other folders not the way - Users should have it all**
* If jumpbox is linux, copy nmap binary and keep it pushing otherwise tunnel nmap like Derron C

(26/11)

* Good job mapping the entire network, we know the structure and names of all machines now, smb and winrm using nxc was mega
* Enumerate FILES02 properly, mimikatz alone won't cut it, the sauce is in Joe's Documents apparently
* Every new user and pass keep cross spraying
* Every password you get spray across ssh, smb, winrm and rdp (winrm and rdp diff holy moly)
* Testing with wmiexec, psexec, or another tool?

(27/11)

* RDP
* bruteforcing user 'offsec' and 'root' when the odds are against you
* hydra -l offsec -P 500-worst-passwords.txt ssh://192.168.142.122
