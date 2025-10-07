# Jordak \[Inter]

## Report

jorani v.1.0 is vulnerable to authenticated RCE which may be buggy but use it to spawn a reverse shell.

env can be run as sudo by user 'jordak' to become root.

## Loot

### Enumeration

192.168.106.109

`http://192.168.106.109/sql/jorani.sql`

jorani v.1.0 after logging in as bbalet

### Attack Vectors

{% embed url="https://github.com/Orange-Cyberdefense/CVE-repository/blob/master/PoCs/CVE_Jorani.py" %}

### PrivEsc

```
User jordak may run the following commands on jordak:
    (ALL : ALL) ALL
    (ALL) NOPASSWD: /usr/bin/env
```

### Creds

bbalet:$2a$08$LeUbaGFqJjLSAN7to9URsuHB41zcmsMBgBhpZuFp2y2OTxtVcMQ.C

bbalet:bbalet

### Proof
