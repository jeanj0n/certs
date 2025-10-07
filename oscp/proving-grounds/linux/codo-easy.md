# Codo \[Easy]

## Report

Codoforum admin panel is vulnerable to unsanitized file upload allowing us to obtain a reverse shell. Reading config.php we find a plaintext password which does not work for user 'offsec' but does for root.

## Cheatsheet

Yeah about that

## Loot

### Enumeration

192.168.53.23

22 and 80

#### 80

Codoforum 5.1 RCE (CVE-2022-31854)

### Attack Vectors

Change logo feature in admin panel is not sanitized allowing us to upload a PHP reverse shell

### PrivEsc

Ideally, I would read the config files manually or linPEAS would come thru but I guess I got a bit cocky lol

<figure><img src="../../.gitbook/assets/image (121).png" alt=""><figcaption></figcaption></figure>

### Creds

None

### Proof

None
