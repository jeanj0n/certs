# WATCH

{% embed url="https://help.offsec.com/hc/en-us/articles/360046293832-Common-VPN-and-Machine-VM-Issues#general-vpn-tips" %}

`sudo ifconfig tun0 mtu 1250`

For every room, mention that room in the associated notes of every section (eg. group policy -> refer this room)

The first room 'Twiggy' showed the Offsec patterns we have to watch for

* revshell only on certain ports (80,8000)
* base64 encode payloads if within quotes - you'll know whats up
* ffuf is not broken, the vpn connection is fine (new syntax port position?)
* default creds - offsec loves admin:admin?
* internal enumeration for AD instead of external (from kali)

TRY HARDER

## What Did We Learn (3/10)

* Your first step if we get stuck is WHERE AND HOW CAN I GET MORE INFO WITH WHAT I HAVE
* Endgame is very weak (SUDO abuse)
* When working with binaries think about the binary itself, when working with a script, please think about the contents of the script
* Standard directories of services may be changed, don't lose the plot
* **To go slow is to go fast - look up once a while when you're carrying the ball**
* Don't forget your HTB tricks just cus we in a diff spot (SQLi and friends)
* Be confident enough that once the path you took didn't work, it's not going to and we need to find the next one
* Organize notes better, chained exploits become difficult if you don't keep track
* Don't be scared of trial and error, you never get things right on the first try
* YOU DO NOT MAKE MORE PROGRESS JUST BECAUSE YOU'RE TICKING OFF MORE BOXES

## Faults So Far

* Googling exploits
* Finding directories and passwords within box
* Check stack and wappalyzer
* One exploit didn't work -> gave up
* One port for revshell didn't work despite various payloads

### Boxes I Had my Ass Handed to ME

* Scrutiny
* BitForge
* Clue
* SPX
* Blackgate

{% embed url="https://www.youtube.com/watch?v=AQwfCRm12hM" %}
I just loved the enumeration process honestly, could learn a thing or two
{% endembed %}
