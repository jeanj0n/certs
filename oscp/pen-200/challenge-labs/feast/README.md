# Feast

## Report

> We have been tasked to conduct a penetration test on the network of _feast.com_. Several vulnerabilities and misconfigurations are present on the Active Directory environment, which can be leveraged by an attacker to gain access to all workstations. The main objective is obtain access to the Domain Controller.
>
> The credentials below can be used to commence your attack: Username: Eric.Wallows Password: EricLikesRunning800

## Domain Users

`Eric.Wallows:EricLikesRunning800`

```
jeff.borrows:naruto
mario.lemieux:newP@ssword2022 [We reset this]
```

## Chain Attack Flow

```
MS01: Upload file to vulnerable S3 bucket with weak auth perms, sync via website and access revshell from '/storage'
MS02: Bloodhound shows user jeff.borrows has GenericAll over mario.lemieux who has Admin and WinRM privs
DC01: Domain administrator has a logged on session in MS02, mimikatz to get pass and voila
```

## Flags

```
MS01 - proof.txt
MS02 - proof.txt
DC01 - proof.txt
```

## Enumeration \[Not Machine Specific]

```
for p in {ftp,mssql,rdp,wmi,ldap,smb,winrm}; do nxc $p 192.168.113.169-171 -u Eric.Wallows -p 'EricLikesRunning800' ; done
```

## Learn

* There was an IP mentioned out of our scope (.168) where the bucket was, ping sweep first

<figure><img src="../../../.gitbook/assets/image (142).png" alt=""><figcaption></figcaption></figure>
