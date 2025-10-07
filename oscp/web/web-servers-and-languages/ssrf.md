# SSRF

{% embed url="https://beauknows.tech/posts/collaborator-alternatives/" %}

### Lab 1

The stock API endpoint URL is modified to `http://localhost` and from the response, we find the API endpoint to delete the user carlos

<figure><img src="../../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Lab 2

Fuzz a range 192.168.0.FUZZ:8080 same admin endpoint and delte user functionality. Worked with and without URL encoding. Intruder is so slow lmao

<figure><img src="../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Lab 3 (Bypass blacklist filters)

Ways of representing an IP to bypass regex

* represent IP in decimal value (127.0.0.1 -> 2130706433)
* nip.io : DNS wildcard that can map any record to any IP, how use tho
* 127.0.0.1 -> 127.1

only 127.1 did work eventually, howver the admin keyword was also blacklisted

To bypass this, first only 'a' was URL encoded and it still returned fail but encoding the encoded character once more did the trick.

There's no hard and fast rule here, try to figure out the logic off the web app by trial and error and response codes/size

<figure><img src="../../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Lab 4 (Filter bypass via Open redirect)

<figure><img src="../../.gitbook/assets/image (10) (1).png" alt=""><figcaption><p>the normal path used by stockapi is URL encoded</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (8) (1).png" alt=""><figcaption><p>Testing SSRF in next-product endpoint, any value we pass in path is redirected to as shown by the 302 code</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>

The stockApi is only allowed to access the local application as seen in the original request body, so we use an endpoint within the local application that is referring to a 'path' and redirects it to the path we specify (internal or external as tested)

Using the next-product endpoint here in stockApi is valid since the URL is within the local application itself and we are not violating any external rules. Now we can bypass the initial check that was put on stockApi

The fun part is you wouldn't be able to exploit this wo proper enumeration so thats the key takeaway here actlly

### Lab 5 (Bypass whitelist filters)
