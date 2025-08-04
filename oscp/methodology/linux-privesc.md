---
description: watch pspy
---

# Linux Privesc

{% embed url="https://github.com/RajChowdhury240/OSCP-CheatSheet/blob/main/Linux%20-%20Privilege%20Escalation.md#systemd-timers" %}

incorrect path, scripts running as root, cronjobs, docker container, git server

there are some crazy techniques ippsec and tj the plugs, we gonna document those and any cool ones we do here

## Checklist

* Kernel and distribution release details
* Can you:
  * Add to sudoers
  * Copy file, change ownership, symlink
  * Revshell
* System Information:
  * Hostname
  * Networking details:
  * Current IP
  * Default route details
  * DNS server information
* User Information:
  * Current user details
  * Last logged on users
  * Shows users logged onto the host
  * List all users including uid/gid information
  * List root accounts
  * Extracts password policies and hash storage method information
  * Checks umask value
  * Checks if password hashes are stored in /etc/passwd
  * Extract full details for 'default' uid's such as 0, 1000, 1001 etc
  * Attempt to read restricted files i.e. /etc/shadow
  * List current users history files (i.e .bash\_history, .nano\_history, .mysql\_history , etc.)
  * Basic SSH checks
* Privileged access:
  * Which users have recently used sudo
  * Determine if /etc/sudoers is accessible
  * Determine if the current user has Sudo access without a password
  * Are known 'good' breakout binaries available via Sudo (i.e. nmap, vim etc.)
  * Is root's home directory accessible
  * List permissions for /home/
* Environmental:
  * Display current $PATH
  * Displays env information
* Jobs/Tasks:
  * List all cron jobs
  * Locate all world-writable cron jobs
  * Locate cron jobs owned by other users of the system
  * List the active and inactive systemd timers
* Services:
  * List network connections (TCP & UDP)
  * List running processes
  * Lookup and list process binaries and associated permissions
  * List inetd.conf/xined.conf contents and associated binary file permissions
  * List init.d binary permissions
* Version Information (of the following):
  * Sudo
  * MYSQL
  * Postgres
  * Apache
    * Checks user config
    * Shows enabled modules
    * Checks for htpasswd files
    * View www directories
* Default/Weak Credentials:
  * Checks for default/weak Postgres accounts
  * Checks for default/weak MYSQL accounts
* Searches:
  * Locate all SUID/GUID files
  * Locate all world-writable SUID/GUID files
  * Locate all SUID/GUID files owned by root
  * Locate 'interesting' SUID/GUID files (i.e. nmap, vim etc)
  * Locate files with POSIX capabilities
  * List all world-writable files
  * Find/list all accessible \*.plan files and display contents
  * Find/list all accessible \*.rhosts files and display contents
  * Show NFS server details
  * Locate \*.conf and \*.log files containing keyword supplied at script runtime
  * List all \*.conf files located in /etc
  * Locate mail

### Sudo and SUID

```
sudo -l
find / -perm -g=s -type f 2>/dev/null    # SGID
find / -perm -u=s -type f 2>/dev/null    # SUID

find / -perm -g=s -o -perm -u=s -type f 2>/dev/null    # SGID or SUID < full search  
for i in `locate -r "bin$"`; do find $i \( -perm -4000 -o -perm -2000 \) -type f 2>/dev/null; done    # Looks in 'common' places: /bin, /sbin < quicker   

find / perm /u=s -user "Username" 2>/dev/null  
```

#### Find SUID root files <a href="#find-suid-root-files" id="find-suid-root-files"></a>

```
find / -user root -perm -4000 -print  2>/dev/null
```

#### Find SGID root files: <a href="#find-sgid-root-files" id="find-sgid-root-files"></a>

```
find / -group root -perm -2000 -print 2>/dev/null
```

#### Find SUID and SGID files owned by anyone: <a href="#find-suid-and-sgid-files-owned-by-anyone" id="find-suid-and-sgid-files-owned-by-anyone"></a>

```
find / -perm -4000 -o -perm -2000 -print  2>/dev/null
```

### Capabilities

```
/usr/bin/getcap -r / 2>/dev/null        # list all 
/usr/bin/setcap -r /bin/ping            # remove
/usr/bin/setcap cap_net_raw+p /bin/ping # add
./python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'
```

### PATH <a href="#kernel" id="kernel"></a>

```
echo $PATH
export PATH=<PATH/TO/FOLDER>:$PATH
#BInary to be called is placed and this path will be looked first
```

### Crontab <a href="#kernel" id="kernel"></a>

```
/etc/init.d
/etc/cron*
/etc/crontab
/etc/cron.allow
/etc/cron.d 
/etc/cron.deny
/etc/cron.daily
/etc/cron.hourly
/etc/cron.monthly
/etc/cron.weekly
/etc/sudoers
/etc/exports
/etc/anacrontab
/var/spool/cron
/var/spool/cron/crontabs/root

crontab -l
ls -alh /var/spool/cron;
ls -al /etc/ | grep cron
ls -al /etc/cron*
cat /etc/cron*
cat /etc/at.allow
cat /etc/at.deny
cat /etc/cron.allow
cat /etc/cron.deny*
```

### Kernel <a href="#kernel" id="kernel"></a>

{% embed url="https://github.com/mzet-/linux-exploit-suggester" %}

{% embed url="https://github.com/jondonas/linux-exploit-suggester-2" %}

```
cat /proc/version
uname -a
uname -ar
uname -mrs
rpm -q kernel
dmesg | grep Linux
ls /boot | grep vmlinuz-
```

{% embed url="https://guif.re/linuxeop" %}

{% embed url="https://book.hacktricks.xyz/linux-hardening/privilege-escalation/wildcards-spare-tricks?source=post_page-----16397895490f--------------------------------" %}
READDDD from HTB Usage
{% endembed %}

{% embed url="https://t0o0tz.com/posts/post0x0004/" %}
