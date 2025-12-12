# PC \[Inter]

## Report

Enumeration shows port 65432 listening on localhost, port forward and nmap shows Uvicorn running, linpeas also shows a python script 'rpc.py' at `/opt/rpc.py` as root. Google search shows RPC RCE is a thing and yes it is vulnerable.

### Hint

Who would've thought to literally google 'rpc.py' exploit lol

## Cheatsheet

```
ssh -i id_rsa -L 65432:127.0.0.1:65432 user@192.168.56.210
RPC v.0.6 exploit
```

## Loot

### Enumeration

192.168.56.210

#### 8000

It's literally a terminal - add SSH keys

#### 65432 (localhost port forward)

Uvicorn - Python ASGI (Asynchronous Server Gateway Interface) web server

### Attack Vectors

```
/opt/rpc.py root root -> Uvicorn
uid=104(syslog) gid=110(syslog) groups=110(syslog),4(adm),5(tty)
tcp        0      0 127.0.0.1:65432         0.0.0.0:*               LISTEN 
```

### PrivEsc

{% code title="rpc.py" %}
```python
from typing import AsyncGenerator
from typing_extensions import TypedDict

import uvicorn
from rpcpy import RPC

app = RPC(mode="ASGI")


@app.register
async def none() -> None:
    return


@app.register
async def sayhi(name: str) -> str:
    return f"hi {name}"


@app.register
async def yield_data(max_num: int) -> AsyncGenerator[int, None]:
    for i in range(max_num):
        yield i


D = TypedDict("D", {"key": str, "other-key": str})


@app.register
async def query_dict(value: str) -> D:
    return {"key": value, "other-key": value}


if __name__ == "__main__":
    uvicorn.run(app, interface="asgi3", port=65432)

```
{% endcode %}

### Creds



### Proof

{% embed url="https://github.com/CSpanias/rpc-rce.py/blob/main/rpc-rce.py" %}

Generate payload and convert to pickle bytes (pickle.dumps)
