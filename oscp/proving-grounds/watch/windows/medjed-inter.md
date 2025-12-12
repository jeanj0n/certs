# Medjed \[Inter]

## Report



## Enumeration

192.168.244.127

```
PORT      STATE SERVICE       VERSION
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
3306/tcp  open  mysql?
| fingerprint-strings: 
|   DNSVersionBindReqTCP, FourOhFourRequest, HTTPOptions, JavaRMI, Kerberos, LANDesk-RC, LPDString, SIPOptions, SMBProgNeg, afp: 
|_    Host '192.168.45.167' is not allowed to connect to this MariaDB server
5040/tcp  open  unknown
7680/tcp  open  pando-pub?
8000/tcp  open  http-alt      BarracudaServer.com (Windows)
| http-webdav-scan: 
|   Server Type: BarracudaServer.com (Windows)
|   WebDAV type: Unknown
|_  Allowed Methods: OPTIONS, GET, HEAD, PROPFIND, PUT, COPY, DELETE, MOVE, MKCOL, PROPFIND, PROPPATCH, LOCK, UNLOCK
| fingerprint-strings: 
|   FourOhFourRequest, Socks5: 
|     HTTP/1.1 200 OK
|     Date: Mon, 27 Oct 2025 13:56:39 GMT
|     Server: BarracudaServer.com (Windows)
|     Connection: Close
|   GenericLines, GetRequest: 
|     HTTP/1.1 200 OK
|     Date: Mon, 27 Oct 2025 13:56:34 GMT
|     Server: BarracudaServer.com (Windows)
|     Connection: Close
|_    <html><body><h1>400 Bad Request</h1>Can't parse request<p>BarracudaServer.com (Windows)</p></body></html>
| http-methods: 
|   Supported Methods: OPTIONS GET HEAD PROPFIND PUT COPY DELETE MOVE MKCOL PROPPATCH LOCK UNLOCK POST
|_  Potentially risky methods: PROPFIND PUT COPY DELETE MOVE MKCOL PROPPATCH LOCK UNLOCK
|_http-server-header: BarracudaServer.com (Windows)
30021/tcp open  ftp           FileZilla ftpd 0.9.41 beta
|_ftp-bounce: bounce working!
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -r--r--r-- 1 ftp ftp            536 Nov 03  2020 .gitignore
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 app
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 bin
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 config
| -r--r--r-- 1 ftp ftp            130 Nov 03  2020 config.ru
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 db
| -r--r--r-- 1 ftp ftp           1750 Nov 03  2020 Gemfile
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 lib
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 log
| -r--r--r-- 1 ftp ftp             66 Nov 03  2020 package.json
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 public
| -r--r--r-- 1 ftp ftp            227 Nov 03  2020 Rakefile
| -r--r--r-- 1 ftp ftp            374 Nov 03  2020 README.md
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 test
| drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 tmp
|_drwxr-xr-x 1 ftp ftp              0 Nov 03  2020 vendor
| ftp-syst: 
|_  SYST: UNIX emulated by FileZilla
33033/tcp open  unknown
| fingerprint-strings: 
|   GenericLines: 
|     HTTP/1.1 400 Bad Request
|   GetRequest, HTTPOptions: 
|     HTTP/1.0 403 Forbidden
|     Content-Type: text/html; charset=UTF-8
|     Content-Length: 3102
|     .details {
|_    bord
44330/tcp open  ssl/unknown
|_ssl-date: 2025-10-27T13:59:35+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=server demo 1024 bits/organizationName=Real Time Logic/stateOrProvinceName=CA/countryName=US
| Issuer: commonName=demo CA/organizationName=Real Time Logic/stateOrProvinceName=CA/countryName=US
| fingerprint-strings: 
|   GenericLines, GetRequest, HTTPOptions: 
|     HTTP/1.1 200 OK
|     Date: Mon, 27 Oct 2025 13:56:46 GMT
|     Server: BarracudaServer.com (Windows)
|     Connection: Close
45332/tcp open  http          Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1g PHP/7.3.23)
| http-methods: 
|   Supported Methods: HEAD GET POST OPTIONS TRACE
|_  Potentially risky methods: TRACE
|_http-title: Quiz App
|_http-server-header: Apache/2.4.46 (Win64) OpenSSL/1.1.1g PHP/7.3.23
45443/tcp open  http          Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1g PHP/7.3.23)
| http-methods: 
|   Supported Methods: HEAD GET POST OPTIONS TRACE
|_  Potentially risky methods: TRACE
|_http-title: Quiz App
|_http-server-header: Apache/2.4.46 (Win64) OpenSSL/1.1.1g PHP/7.3.23
```

#### Barracuda (HTTP/HTTPS) 44330 & 8000

Potential SYSTEM PrivEsc (BarracudaDrive)

{% embed url="https://github.com/boku7/BarracudaDrivev6.5-LocalPrivEsc/blob/master/barracudaDrive6.5-PrivEsc.txt" %}

We move

Web File Manager - can we upload and do sum?

is WebDAV the way? we dont know right put asp reverse shell id kms lol

#### Quiz App (45332/45443) HTTP

tf is this lol, analyze source maybe? cus i aint seeing anything here

#### 33033 (Medjed)

Login and users page, password spray?



### Files



### Initial Foothold

Possible vectors

Login through the portal

Forget password using all sentences mentioned below

WebDAV&#x20;

Inspect Quiz app

## PrivEsc



## Loot

### Creds



### Flags

local:464979ad64b321ede93ebe6ea29c9a99

proof:2de0ef836aaa323a42138420af0fe51f

## Proof
