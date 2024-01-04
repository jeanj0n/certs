# Security Protocols

TLS more secure than SSL, newer also

**Downgrade Attack -** When a protocol is tricked to use a lower version of itself instead of high quality, prevent by telling server not to comm w lower vesion ppl

SSH 2.0 uses Diffie-Hellman and MACs (Message Access Control), got rid of unauthorized insertion issues and int overflows

### VPN Protocols

* PPTP (Point to Point Tunneling Protocol) **1723** - encapsulate PPP packets and send data as encrypted traffic, uses CHAP for auth
* L2TP (Layer 2 Tunneling Protocol) **1701** - No security of its own, usually paired with IPSec
* IPSec - TCP/IP protocol that authenticates and encrypts IP packets and secure comms bw devices using this, perform key exchange for auth
* IKE/ISAKMP (Internet Key Exchange) - used by IPSec to create a secure tunnel for authenticated peers\
  Three Modes: Main, Aggressive and Quick

### Working of IKE to setup IPSec tunnel

Main mode uses three exchanges, aggressive uses only three packets but same effect as main.\
Quick only deals with negotiated params of the tunnel

1. Phase 1 - Establish encryption and auth protocols bw systems, use pre-shared key or certificates.\
   Then use ISAKMP to create SA, each side create private key and make a public one through Diffie-Hellman and finally key exchange happens
2. Phase 2 - The shared key is then used for encryption and integrity protocols within that IPSec layer, a tunnel inside a tunnel and now data can flow securely in both directions

**Security Association (SA)** - Trusting secure connections and security info using certificates or crypto keys

**Authentication Header (AH)** - Used in IPSec to provide integrity and auth

**Encapsulating Security Payload (ESP)** - integirty, confidentiality and auth of packets&#x20;

IPSec can be in two modes

* Transport - host to host mode only uses encryption of payload of IP packet but not its header, should only be used for hosts in a private network
* Tunnel - encrypts the entire payload and header, nobody can see where it going unlike transport and nobody can see contents in both modes&#x20;

