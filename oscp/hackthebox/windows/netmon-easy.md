---
description: Following an IppSec tutorial for this one, nothing from me
---

# Netmon \[WIndows Easy]

Nmap scan shows C drive is exposed via ftp (anonymous access allowed)

Webservers always have config files and w ftp we can read that

Pull up an LFI list since we have access to all local files so might get some juicy stuff

Get user.txt from desktop calm

Now apart from this he also grabs Windows\WindowsUpdate.log find the patch level so exploits after that can be used

Windows\license.rtf mentions the version of windows running too

List of files downloaded from ftp

* hosts
* license.rtf
* user.txt
* WindowsUpdate.log
* win.ini

license.rtf shows WIndows 2016 is running

Update.log doesn't show anything unfortunately

Went to the website, saw a PRTG Monitor

tried default creds, didnt work so looked for config file location online and pulled all three (.dat, .old and .old.bak) it from ftp

go through it (manually or grep before and after) to find creds, login and theres a vuln that allows OS injection, use it to execute a powershell script reverse shell for windows

**IEX syntax write, the command is right there**

the command was also encode, prolly cus AV evasion thats my best guess and yeah

Even tho the command execution part messed up many times, stay calm

First thought port 9001 was the problem cus firewalls only allow you to interact w standard ports such as 80, 443 etc.
