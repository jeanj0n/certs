# Keeper \[Linux Easy]

## Report

FInd default creds for web app, see an existing ticket raised and find user lnorgaard creds via 'Edit' feature. Login via ssh and you get user.txt and a zip file which contains a memdump and password file

Exploit keepass CVE to uncover passwords from memory, contains characters outside English so doesn't show the entire thing properly but a google search with the remaining characters gives the output.

Find puTTY key for root from keepass CLI, convert to SSH for root

## Cheatsheert

```
kpcli -kdb <.kdbx file>
putty to ssh key conversion, both private and public key
chmod 600 id_dsa
```

