# LinkVortex \[Linux Easy]

## Report

A publicly exposed git repository on the dev virtual host exposes password for authentication to ghost CMS on main website. Retrieve the entire repo with that git expose script.&#x20;

Fuzz for directories and find the login page. Google shows CVE for ghost CMS that performs remote file reading.&#x20;

Use this to test passwd file first and then get the config file (there will be on eused for every server deployed). Google the config file, however instead of being in /www its in /var. Get creds for user bob

Bob can run a script as sudo which removes symlinks. The primary logic is file must be a png first of all, it can't directly read sensitive info so if our target has root or etc in target path, it's removed and displays output of target link otherwise. Also a variable CHECK\_CONTENT must be true

Make a symlink first to /root/root.txt and a second one pointing to this first symlink. We are root.

## Cheatsheet

```
nmap
ffuf
gobuster
wfuzz
Git repo clone
CVE file read
ln -s /home/bob/wizard.txt wizard.png
```

```
#!/bin/bash

QUAR_DIR="/var/quarantined"

if [ -z $CHECK_CONTENT ];then
  CHECK_CONTENT=false
fi

LINK=$1

if ! [[ "$LINK" =~ \.png$ ]]; then
  /usr/bin/echo "! First argument must be a png file !"
  exit 2
fi

if /usr/bin/sudo /usr/bin/test -L $LINK;then
  LINK_NAME=$(/usr/bin/basename $LINK)
  LINK_TARGET=$(/usr/bin/readlink $LINK)
  if /usr/bin/echo "$LINK_TARGET" | /usr/bin/grep -Eq '(etc|root)';then
    /usr/bin/echo "! Trying to read critical files, removing link [ $LINK ] !"
    /usr/bin/unlink $LINK
  else
    /usr/bin/echo "Link found [ $LINK ] , moving it to quarantine"
    /usr/bin/mv $LINK $QUAR_DIR/
    if $CHECK_CONTENT;then
      /usr/bin/echo "Content:"
      /usr/bin/cat $QUAR_DIR/$LINK_NAME 2>/dev/null
    fi
  fi
fi
```

## Proof

299cdb4387763f850887275a716153e84793077d - git head for dev vhost

User-agent: \* Sitemap: http://linkvortex.htb/sitemap.xml&#x20;

Disallow: /ghost/ \
Disallow: /p/ \
Disallow: /email/ \
Disallow: /r/

From git logs: dev@linkvortex.htb

Password: OctopiFociPilfer45

default: /var/www/ghost/config.production.json \
actual: /var/lib/ghost/config.production.json

"user": "bob@linkvortex.htb", "pass": "fibber-talented-worth"

## Moral

* That git clone shi was crazy, use it for ANY publicly exposed repo
* Shouldn't have started tweaking at the root part
* Good job enumerating everything, you technically did find the git repo but we move
* Once exploit is done, work around the payloads yourself, ease out



