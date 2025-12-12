# Underpass \[Easy]

## Report

Perform SNMP (161/UDP) scanning to find a daloradius server running on host. Using google find basic info about the directory possible (/daloradius). From there, start fuzzing cus as we've seen before it keeps switching up. Find the login panel and use default creds to find a user svcMosh with MD5 password.

svcMosh can run mosh as sudo. Mosh is basically a remote terminal and after doing some research you figure out how to use the command properly.

The catch here is to run the server and connect on that localhost using the key provided. Funny thing is I thought of this but got stumped at the key part cus my stupid ass didn't clock that the key was provided at server command execution.

## Cheatsheet

```
sudo usr/bin/mosh-server [This will generate the key along with success msg]
MOSH_KEY=yOnR7II2oWJEwKvTrySv8w mosh-client 127.0.0.1 60001
```

## Proof

`mosh --server="sudo /usr/bin/mosh-server" localhost`

```
You can see that the --server parameter command defaults to mosh-server, and the super privilege command owned by the current user is also this.
Therefore you can connect to yourself through this parameter
```

<figure><img src="../../../.gitbook/assets/image (86).png" alt=""><figcaption><p>unnecessary info ffs</p></figcaption></figure>

412DD4759978ACFCC81DEAB01B382403: underwaterfriends
