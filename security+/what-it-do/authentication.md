# Authentication

### Auth Models

* Context-aware auth : checks user/system characteristics before connecting (restrict based on time or day)
* Single sign-on (SSO) : one login for all diff resources required to access
* Federated Identity Management (FIdM) - single identity created for a user and shared for all organizations in a federation, this id can be used across the whole federation\
  \
  Ways of implenting\
  1\. Cross-certification : web of trust bw organization where each one certify others in federation(like full mesh network, only good for small number)\
  2.Trusted third party : organizations place trust in a single third party, more efficient\
  **SAML support FIdM to perform SSO**\
  **OpenID(eg. Google sign-in)** also support, user logs into Identity Provider(IP) and access Relying Parties(RP)

**SSO single domain, FIdM multiple domain**

### 802.1x

Standard port based auth for wired and wireless networks, can prevent rogue devices

![](<../../.gitbook/assets/image (51).png>)

Encapsulate EAP(Extensible Authentication Protocol) - A framework of protocols allow numerous methods of authentication

* EAP-MD5 uses simple passwords for its challenge-authentication
* EAP-TLS uses digital certificates for mutual authentication
* EAP-TTLS uses a server-side digital certificate and a client-side password for mutual authentication

<div align="left">

<figure><img src="../../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

</div>

### LDAP (Lightweight Directory Access Protocol)&#x20;

database which centralize info about clients and objects in network

389 Unecnrypted and 636 Encrypted over SSL

Active Directory - Microsoft implementation along with SSO

**Kerboros -** Auth protocol used by Windows to provide two-way authentication using a system of **tickets**, run on **port 88**&#x20;

![](<../../.gitbook/assets/image (53).png>)

RDP run on 3389

VNC (Virtual Network Computer) - Cross platform of RDP need client, server and protocol to be configured, run on 5900

#### Remote Access Services

PAP - transmits the login credentials unencrypted

CHAP - server send random numbers, client encrypt and send back, server decrypt and check if result is same as what it sent (password stored in server)

MS-CHAP is Microsoft Version of CHAP\
Both of these were used in dial-up so not imp anymore

#### VPN

VPN Concentrator - server at centre to handle all VPN requests

Ensure **Split Tunnelling** doesn't occur, could bypass lot of security measures

#### RADIUS (Remote Authentication Dial-in User Service)&#x20;

Provide centralized authentication of dial-up, VPN and wireless authentication services for 802.1x and EAP

**Perform AAA functions**

Authentication and Authorization - 1812 and 1813

Some versions also use 1645 and 1646

#### TACACS+ (Cisco proprietary) (Terminal Access Controller Access Control System+)

uses TCP whereas RADIUS use UDP

Port 49

Supports all protocols, RADIUS doesn't support Remote access protocol and NetBIOS

802.1x exec Port based Network Access Control (PNAC)

