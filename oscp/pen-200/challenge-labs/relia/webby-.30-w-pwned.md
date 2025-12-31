# WEBBY .30 \[W, Pwned]

## Enumeration

```
Nmap scan report for 172.16.162.30
Host is up (0.000086s latency).
Not shown: 65520 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE       VERSION
80/tcp    open  http          Microsoft IIS httpd 10.0
|_http-title: Anna Test Machine
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=WEBBY.relia.com
| Issuer: commonName=WEBBY.relia.com
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-12-19T05:03:05
| Not valid after:  2026-06-20T05:03:05
| MD5:   bea9:db35:fff6:5ff2:28e7:5ac0:e7c6:4b8a
|_SHA-1: 5e2c:302b:da88:6d79:2145:f65e:d8a4:1c1c:5a21:9f1b
|_ssl-date: 2025-12-20T09:35:16+00:00; +1s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: RELIA
|   NetBIOS_Domain_Name: RELIA
|   NetBIOS_Computer_Name: WEBBY
|   DNS_Domain_Name: relia.com
|   DNS_Computer_Name: WEBBY.relia.com
|   DNS_Tree_Name: relia.com
|   Product_Version: 10.0.20348
|_  System_Time: 2025-12-20T09:34:10+00:00
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
27017/tcp open  mongodb       MongoDB 6.0.2
| fingerprint-strings: 
|   FourOhFourRequest, GetRequest: 
|     HTTP/1.0 200 OK
|     Connection: close
|     Content-Type: text/plain
|     Content-Length: 85
|     looks like you are trying to access MongoDB over HTTP on the native driver port.
|   mongodb: 
|     errmsg
|     Unsupported OP_QUERY command: serverStatus. The client driver may require an upgrade. For more details see https://dochub.mongodb.org/core/legacy-opcode-removal
|     code
|     codeName
|_    UnsupportedOpQueryCommand
| mongodb-info: 
|   MongoDB Build info
|     ok = 1.0
|     debug = false
|     gitVersion = 94fb7dfc8b974f1f5343e7ea394d0d9deedba50e
|     maxBsonObjectSize = 16777216
|     openssl
|       running = Windows SChannel
|     storageEngines
|       1 = ephemeralForTest
|       2 = wiredTiger
|       0 = devnull
|     sysInfo = deprecated
|     allocator = tcmalloc
|     version = 6.0.2
|     bits = 64
|     buildEnvironment
|       cc = cl: Microsoft (R) C/C++ Optimizing Compiler Version 19.31.31107 for x64
|       ccflags = /nologo /WX /FImongo/platform/basic.h /fp:strict /EHsc /W3 /wd4068 /wd4244 /wd4267 /wd4290 /wd4351 /wd4355 /wd4373 /wd4800 /wd4251 /wd4291 /we4013 /we4099 /we4930 /errorReport:none /MD /O2 /Oy- /bigobj /utf-8 /permissive- /Zc:__cplusplus /Zc:sizedDealloc /volatile:iso /diagnostics:caret /std:c++17 /Gw /Gy /Zc:inline
|       distarch = x86_64
|       target_arch = x86_64
|       linkflags = /nologo /DEBUG /INCREMENTAL:NO /LARGEADDRESSAWARE /OPT:REF
|       cppdefines = SAFEINT_USE_INTRINSICS 0 PCRE_STATIC NDEBUG BOOST_ALL_NO_LIB _UNICODE UNICODE _SILENCE_CXX17_ALLOCATOR_VOID_DEPRECATION_WARNING _SILENCE_CXX17_OLD_ALLOCATOR_MEMBERS_DEPRECATION_WARNING _SILENCE_CXX17_CODECVT_HEADER_DEPRECATION_WARNING _SILENCE_ALL_CXX20_DEPRECATION_WARNINGS _CONSOLE _CRT_SECURE_NO_WARNINGS _ENABLE_EXTENDED_ALIGNED_STORAGE _SCL_SECURE_NO_WARNINGS _WIN32_WINNT 0x0A00 BOOST_USE_WINAPI_VERSION 0x0A00 NTDDI_VERSION 0x0A000000 BOOST_THREAD_VERSION 5 BOOST_THREAD_USES_DATETIME BOOST_SYSTEM_NO_DEPRECATED BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS BOOST_ENABLE_ASSERT_DEBUG_HANDLER BOOST_LOG_NO_SHORTHAND_NAMES BOOST_LOG_USE_NATIVE_SYSLOG BOOST_LOG_WITHOUT_THREAD_ATTR ABSL_FORCE_ALIGNED_ACCESS
|       distmod = windows
|       target_os = windows
|       cxxflags = /TP
|       cxx = cl: Microsoft (R) C/C++ Optimizing Compiler Version 19.31.31107 for x64
|     javascriptEngine = mozjs
|     versionArray
|       1 = 0
|       2 = 2
|       3 = 0
|       0 = 6
|     targetMinOS = Windows 7/Windows Server 2008 R2
|     modules
|   Server status
|     ok = 0.0
|     code = 352
|     errmsg = Unsupported OP_QUERY command: serverStatus. The client driver may require an upgrade. For more details see https://dochub.mongodb.org/core/legacy-opcode-removal
|_    codeName = UnsupportedOpQueryCommand
| mongodb-databases: 
|   ok = 0.0
|   code = 352
|   errmsg = Unsupported OP_QUERY command: listDatabases. The client driver may require an upgrade. For more details see https://dochub.mongodb.org/core/legacy-opcode-removal
|_  codeName = UnsupportedOpQueryCommand
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49670/tcp open  msrpc         Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2025-12-20T09:34:11
|_  start_date: N/A
| nbstat: NetBIOS name: WEBBY, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:ab:b9:36 (VMware)
| Names:
|   WEBBY<20>            Flags: <unique><active>
|   WEBBY<00>            Flags: <unique><active>
|_  RELIA<00>            Flags: <group><active>

```

### Files

```
```

### Ports

### Exploits

### Loot

## Initial Foothold

Got domain admin creds from FILES

## PrivEsc

Read the above line

## Post Exploitation

None
