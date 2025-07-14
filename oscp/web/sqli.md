# SQLi



{% embed url="https://github.com/codingo/OSCP-2/blob/master/Documents/SQL%20Injection%20Cheatsheet.md" %}

{% embed url="https://infosecwriteups.com/how-to-learn-manual-sql-injection-for-oscp-step-by-step-f447d3f72ad5" %}

{% embed url="https://github.com/oncybersec/time-blind-sqli/tree/main" %}

Lock in w me twin

```
information_schema.schemata : schema_name
information_schema.tables : table_name
information_schema.columns : column_name
```

## UDF (User Defined Function)

SQLI -> execute commands within SQL command, transfer and use a reverse shell

{% embed url="https://sudsy-fireplace-912.notion.site/Pebbles-from-Proving-Grounds-without-SQLMap-by-Luis-Moret-lainkusanagi-23b29df77e6946a6bb8cb213a76a9ac8" %}

{% embed url="https://medium.com/r3d-buck3t/privilege-escalation-with-mysql-user-defined-functions-996ef7d5ceaf" %}

{% embed url="https://r3dbuck3t.notion.site/MySQL-UDF-functions-3542db4a45ba4b7ea9b946098f9f80a3" %}

### Rana's Playlist

{% embed url="https://www.youtube.com/playlist?list=PLuyTk2_mYISLaZC4fVqDuW_hOk0dd5rlf" %}
Walkthroughs of all PortSwigger SQLi labs
{% endembed %}

#### Lab 4

For UNION attacks to work, the number of columns selected must be the same as original query and the data type used as well eg. if second column of original query uses text your UNION second column must do the same.

`GIfts' order by 4—` (order by also helps determine number of columns apart from the NULL method, whichever number used that columns is used for grouping as seen)

#### Lab 6 (Multiple values in single column)

select concat(user, ':', password) from users--\
select user || ':' || password from users--

Both do the same thing (incase of keyword filters)

#### Lab 11 (Blind w conditional responses)

Burp intruder clusterbomb allocates diff payload sets to diff payload positions and performs all combinations exactly what we need (ASCII of all values and numbers to iterate positions)

#### Lab 12

I didnt understand lmao

#### Lab 15 & 16&#x20;

wtf is out of band interaction fr

