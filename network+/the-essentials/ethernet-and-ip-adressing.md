# Ethernet and IP Adressing

### Cabling

Crossover cable (T-568-B to A) is used when same type of device is used( Data Terminating Equipment (DTE) or Data Communications Equipment (DCE))

*   Computer to switch

    Router to modem o Crossover Cables
* Used to connect a switch to a switch , Not required if switch support MDIX
* Plenum: Fire resistant cabling, used for cables you cant see behind the walls

Punch Down block: 66 not a great choice, bad interference till CAT5, 110 after CAT5

![](<../../.gitbook/assets/image (36).png>)

Ethernet IEEE

* 802.3ad: link aggregation
* 802.3af: PoE, 802.3at: PoE+
* 802.1x: user authentication
* 802.1d: spanning tree protocol
* 802.1q: vlan trunking

**IP Addressing**

* network bit and host bit(empty portion)
* number of host bits borrowed needed for no of subnets created
* number of remaining host bits needed for no of IP available
* VLSM: variable subnet making(subnetting a subnet)
* THE GATEWAY MUST BE IN THE SAME SUBNET AS DEVICE TRYING TO CONNECT TO INTERNET

### Classes of IP addresses

IPv4 4 groups( 1 has 8 bits) of 4 decimal separated by .

IPv6(Supports MTU) uses 8 groups(1 has 16 bits) of 4 hexa digits separated by :

if all 0000 and stuff like that, notation can be collapsed (eg. ABCE::2222:5678)

MAC Address has 6 groups of two hexa each separated by :

Public

![](<../../.gitbook/assets/image (32) (1).png>)

Private

![](<../../.gitbook/assets/image (35).png>)

Special

![](<../../.gitbook/assets/image (45).png>)

* APIPA: when device has no static address and CANT connect to DHCP, address from 169.-- is assigned, quick config of LAN, non-routable but local subnet is connected
* Zeroconf: based on APIPA, resolve comp name to IP address without DNS server on local, provide service discovery protocols(printers etc)

### Subnetting

![](<../../.gitbook/assets/image (34).png>)

The Mask value from left to right is previous mask + corrsponding host (Class C subnetting)
