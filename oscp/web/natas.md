# Natas

### 9 & 10

<div align="left"><figure><img src="../.gitbook/assets/image (83).png" alt="" width="417"><figcaption></figcaption></figure></div>

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
