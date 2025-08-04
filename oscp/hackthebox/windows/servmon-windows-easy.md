# ServMon \[Windows Easy]

{% embed url="https://ivanitlearning.wordpress.com/2020/09/03/hackthebox-servmon/" %}

{% embed url="https://0xdf.gitlab.io/2020/06/20/htb-servmon.html" %}

## Report

I paid time to see how the first website worked but not the second one which was the juicy bit

ALso you brute all for nathan but not nadine

didnt even try to fuzz anything, big disappointment

Can't really do two box in a day if this is the effort we're gonna put for the second one

## Cheatsheet

```
wget -r ftp://anonymous:@10.10.10.184 
https://10.10.10.184:8443 

Brute Force SMB access
crackmapexec smb 10.10.10.184 -u users -p passwords
```

## Proof

FTP supports anonymous login

SMB anon login not possible

LFI CVE found for NVSM but what file to look for?
