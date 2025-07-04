---
description: We stealing S1REN flow w this one
---

# Greenhorn \[Linux Easy]

## Report

Website running pluck, find a CVE online from which we obtain a reverse shell.

Search that area only (you've encountered this same approach multiple times by now) to find a login.php which contains hash in MD5, crack it to find creds surprisingly for admin in pluck and junior in our target for user.

Find a pdf file which has the password blurred out, use a tool to convert to image and then find plaintext from the pixelated image for root.

### Cheatsheet

```
nmap -sV -sC 10.10.11.25 -v
ffuf -w /usr/share/wordlists/bitquark-subdomains-top100000.txt -H "Host: FUZZ.greenhorn.htb" -u http://greenhorn.htb -mc 200 
ffuf -u http://greenhorn.htb/FUZZ -w common.txt -e .php,.html,.txt -mc 200
git clone https://github.com/Rai2en/CVE-2023-50564_Pluck-v4.7.18_PoC
python3 poc.py
sudo apt-get install poppler-utils (for pdfimages)
pdfimages hi.pdf wassup
git clone https://github.com/spipm/Depix
python3 depix.py -p wassup-000.ppm -s images/searchimages/debruinseq_notepad_Windows10_closeAndSpaced.png -o pass.png
```

## Loot

### Hashes

MD5 hash from login.php - for user 'junior'

### Passwords

Crack the MD5, that's all you really need

### Proof/ Flags/ Other

Port 3000 open (from nmap scan) running another http server

Didnt really need to run a file fuzz because from CVE research that pluck has login.php and admin.php
