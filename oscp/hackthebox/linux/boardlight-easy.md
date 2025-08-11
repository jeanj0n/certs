# Boardlight \[Linux Easy]

Fuzzing the vhost was big w, we get a login which had a public exploit for RCE so calm

Pretty sure LinPeas came thru here too for conf files but just in case, this is where the creds were

```
cat /var/www/html/crm.board.htb/htdocs/conf/conf.php 
```

LinPeas came through w the SUID misconfiguration for 'enlightenment'. Public CVE, exploit it and you are root

### Status Takeaway (29/7)

* Feeling more confident in the enumeration side of things, prolly cus im still dealing w easy boxes but feels good. (fuzzing in particular)
* Transferring LinPeas is no big deal now, gotta learn to understand the info out of it, keep a track of all the diff vectors used so far so you can guess wassup yk the intuition ting.
* Editorial was a proper pain in the ass, keep practising Natas
* _`find`_` ``. -name thisfile.txt`
