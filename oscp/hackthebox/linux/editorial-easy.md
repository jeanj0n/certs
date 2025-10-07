# Editorial \[Easy]

Honsetly mad disappointing absolute fumble from all ends

{% embed url="https://benheater.com/hackthebox-editorial/" %}
The methodology and ffuf to fuzz ports
{% endembed %}

<figure><img src="../../.gitbook/assets/image (9) (1) (1).png" alt=""><figcaption><p>To ensure only requests from the target jost show up, add to scope too</p></figcaption></figure>

Now the fact that we couldn't even identify the Preview button actually sent a request of its own is an abomination.

The rest of the form is submitted when we click on Submit Book Info, the above row is what we need. Why you ask?

Because the first field is the one asking for a URL - where we attack using SSRF

Any input we provide gets us a static image, the same one everytime

http://127.0.0.1 is the way but no change, maybe we gotta hit the right port? The application could refer to its own shi from somewhere else yfm.

**So we add a port variable on burp and send it to intruder. How did you do it using ffuf?**

**Also for some reason, burp doesn't show the modified content when viewing past requests but if you send it to repeater, it show up which is nasty work.**

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

But we finally get a hit and that's port 5000. Filter it by the response size easiest option.

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

we get a few API endpoints we can hit up, if you use common sense from reading the content, you can tell which one you really want. Append this to the lo URL we been using as payload to get another document which exposes dev credentials

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

Getting into dev, we see we can't run sudo on anything and no real juice from linpeas.sh either.

But what we do see is a git repository. and this has some nice stuff.\
Go through each file to see if any useful info, i kinda saw that some changes had to be made to app.py but couldn't find it anywhere.

And that's all good only nothing wrong w manually searching

`git show <hash>` shows the commits made, the difference pushed and any comments too.

<figure><img src="../../.gitbook/assets/image (10) (1) (1).png" alt=""><figcaption></figcaption></figure>

Why did I pick this specific hash? Well the comment associated with this hash/ push request mentioned downgrading credentials so ye.

Now becoming prod, we can run sudo on a python script, turns out it's a git clone CVE, take your time to do shit man you'll get it calm

```
sudo /usr/bin/python3 /opt/internal_apps/clone_changes/clone_prod_change.py 'ext::sh -c cat% root/root.txt% >% /tmp/root'
sudo /usr/bin/python3 /opt/internal_apps/clone_changes/clone_prod_change.py 'ext::sh -c cat% /root/root.txt% >% /tmp/root'
```

one / can change everything
