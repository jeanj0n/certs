# Laser

## Report

> We have been tasked to conduct a penetration test on the network of _laser.com_. Several vulnerabilities and misconfigurations are present on the Active Directory environment, which can be leveraged by an attacker to gain access to all workstations. The main objective is obtain access to the Domain Controller.
>
> Although this Challenge Lab is not a mock exam, it has been setup to use the 'Assumed Breach' scenario as seen in the OSCP+ exam. The credentials below can be used to commence your attack: Username: Eric.Wallows Password: EricLikesRunning800

## Domain Users

`Eric.Wallows:EricLikesRunning800`

```
yulia.weber:Yulia@Laser777
boris.crawford:zxcvbnm
```

## Chain Attack Flow

{% code overflow="wrap" %}
```
MS01-MS02: NTLM relay attack to MS02 retrieve creds for local admin, disable defender and find pcap file exposing creds for 'yulia.weber'
DC01: bloodhound show 'yulia.weber' has GenericAll over 'boris.crawford', set SPN and crack hash, boris is DC admin
```
{% endcode %}

## Flags

```
MS02: proof.txt
DC01: local.txt and proof.txt
```

## Enumeration \[Not Machine Specific]

```
for p in {ftp,mssql,rdp,wmi,ldap,smb,winrm}; do nxc $p 192.168.113.169-171 -u Eric.Wallows -p 'EricLikesRunning800' ; done
```

## Learn

* smbclient using domain name finally
* ntlmrelay without powershell revshell
* disabling defender
* setting custom SPN and cracking boris.crawford password

> Hey, @rootkid ![👋](https://discord.com/assets/82c4e269c8f910a4.svg) . Bloodhound is showing you that **yulia.weber** has _GenericWrite_ over **boris.crawford**, but this doesn't mean that **boris.crawford** MUST have an [SPN ](https://learn.microsoft.com/en-us/windows/win32/ad/service-principal-names)set on it. However, you have the correct ideas, you just need to piece them together. Meaning, [_GenericWrite_](https://bloodhound.specterops.io/resources/edges/generic-write) _grants you the ability to write to any non-protected attribute on the target object, including “members” for a group, and “servicePrincipalNames” for a user_. Therefore, you could set the SPN by yourself (for which you can use **Rubeus** for example), and then, perform the Kerberoast
