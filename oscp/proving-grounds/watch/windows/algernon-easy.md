# Algernon \[Easy]

## Report

SmarterMail running on port 9998 is vulnerable to CVE-2019-7214, run exploit to get reverse shell as SYSTEM

## Enumeration

192.168.55.65

```
sudo nmap -Pn -n $IP -sC -sV -p- --open -oN tcpscan.nmap
sudo nmap -sVC -vvv 192.168.116.40 --script vuln -oN fulltcpscan.nmap
```

> I start nearly every box this way because it quickly returns a wealth of information. Sudo as it defaults to the faster half-open SYN scan, then -Pn to ignore ping and assume it is up, -n to ignore DNS, the IP address, -sC for default scripts, -sV for version information, -p- to scan all ports, and MOST importantly the — open argument to only apply scripts and version scans to found open ports.

### Files

#### FTP

Log files show admin login

```
cat 2020.05.12-administrative.log 
03:35:45.726 [192.168.118.6] User @ calling create primary system admin, username: admin
03:35:47.054 [192.168.118.6] Webmail Attempting to login user: admin
03:35:47.054 [192.168.118.6] Webmail Login successful: With user admin
03:35:55.820 [192.168.118.6] Webmail Attempting to login user: admin
03:35:55.820 [192.168.118.6] Webmail Login successful: With user admin
03:36:00.195 [192.168.118.6] User admin@ calling set setup wizard settings
03:36:08.242 [192.168.118.6] User admin@ logging out
```

### Initial Foothold

None

## PrivEsc

SmarterMail running on port 9998 is vulnerable to .NET deserialization

## Loot

### Creds

None

### Flags

ba15db0f4521c3736bdd812453ede964

## Proof

None
