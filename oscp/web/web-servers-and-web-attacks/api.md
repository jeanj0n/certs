# API

{% embed url="https://github.com/danielmiessler/SecLists/tree/master/Discovery/Web-Content/api" %}

{% hint style="info" %}
Monitored \[HTB Linux]
{% endhint %}

Actions and objects (verbs and nouns respecitvely) are important to scope the full functionality of an API endpoint available if the documentation is horrible.

You might need to be authenticated, a value such as an apikey may be required for successful fuzzing

Change request methods to see if the same endpoint works

```
feroxbuster -u https://nagios.monitored.htb/nagiosxi/api -m GET,POST -k
feroxbuster -u https://nagios.monitored.htb/nagiosxi/api/v1 -k --query apikey=IudGPHd9pEKiee9MkJ7ggPD89q3YndctnPeRQOmS2PQ7QIrbJEomFVG6Eut9CHLL -w /opt/SecLists/Discovery/Web-Content/api/objects.txt

For GET do as is standard stuff
ffuf -w [wordlist] -u [URL] -X POST -d 'id=FUZZ' -H 'header: value' -fs xxx  
(Use raw request) : ffuf -request request.txt -request-proto http[s] -w wordlist.txt -r [FUZZ should be present in the request]
```

<figure><img src="../../.gitbook/assets/image (110).png" alt=""><figcaption></figcaption></figure>

<div align="left"><figure><img src="../../.gitbook/assets/image (111).png" alt=""><figcaption><p>Even a '/' makes a big difference</p></figcaption></figure></div>
