# Zeus

## Report

IP=192.168.45.167 \[ours]

## Chain Attack Flow

```
```

## Flags

```
```

## Proof

```
.120 - proof only

```

## Enumeration \[Not Machine Specific]

enumerate users

smb

spray, reuse

```
nxc smb 192.168.178.158-160 -u guest -p '' 
SMB         192.168.178.158 445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:zeus.corp) (signing:True) (SMBv1:False)
SMB         192.168.178.160 445    CLIENT02         [*] Windows 10 / Server 2019 Build 19041 x64 (name:CLIENT02) (domain:zeus.corp) (signing:False) (SMBv1:F
SMB         192.168.178.159 445    CLIENT01         [*] Windows 10 / Server 2019 Build 19041 x64 (name:CLIENT01) (domain:zeus.corp) (signing:False) (SMBv1:F
SMB         192.168.178.158 445    DC01             [+] zeus.corp\guest: 
SMB         192.168.178.160 445    CLIENT02         [+] zeus.corp\guest: 
SMB         192.168.178.159 445    CLIENT01         [+] zeus.corp\guest: 
```

## Learn

* Diff shells have differenet privs what??? the RunasCs shell for the last user didn't have Backup privs but Winrm did that's crazy
