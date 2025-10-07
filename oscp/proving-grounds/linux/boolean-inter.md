# Boolean \[Inter]

## Report

A file manager web application is auth bypassed by modifying the request JSON values when trying to change the email of registered user and is vulnerable to LFI expsoing user 'remi' SSH folder. Existing keys did not work so we leverage access by adding our own.

Root SSH key is also present in remi's SSH folder and use it to become root.

### Hint

* Check working of endpoints via burp as well - we missed the response of email verification and missed the 'confirmed' field
* Realized files were only downloading but could not clock LFI instead tried to upload a revshell
* Forgot how authorized\_keys work come on man

## Cheatsheet

```
#Test for LFI
http://192.168.115.231/?cwd=../../../../../etc&file=passwd&download=true
http://192.168.115.231/?cwd=../../../../../home/remi/.ssh
Upload authorized_keys
ssh -o IdentitiesOnly=yes -i /home/remi/.ssh/keys/root root@127.0.0.1
```

## Loot

### Enumeration

192.168.115.231

Stack - ruby

<div align="left"><figure><img src="../../.gitbook/assets/image (125).png" alt="" width="349"><figcaption><p>Error message</p></figcaption></figure></div>

New port 33017: development server apache

### Attack Vectors

Adding user to website - edit data in web request when trying to change email

<div align="left"><figure><img src="../../.gitbook/assets/image (126).png" alt="" width="563"><figcaption></figcaption></figure></div>



### PrivEsc

Root SSH keys present in user remi SSH folder, however too many keys prevented the normal command from working

### Creds

None

### Proof
