# Wireless Security

WEP (Wired Equivalent Privacy, 802.11 standard)

* static 40 bit pre shared encrypt key
* 24 bit initialization vector (IV) in clear text, NOT GOOD

Wifi Protected Access (WPA)

* TKIP(Temporary Key Initialization Protocol): 48 bit instead of IV
* RC4 used for encryption and adds Message Integrity Check (MIC)

WPA2 (802.11i standard)

* Integrity check through CCMP
* AES (Advanced Encryption Standard): 128 bit or above

802.1x (User Authentication)

* EAP (Extensible Authentication Protocol) used for this

Wifi Protected Setup (WPS) - Automated encryption setup for wireless networks using 8 digit PIN furthered split into 2 blocksbut very flawed, don't even need password to gain access

airodump-ng, airreplay-ng and aircrack-ng dangerous tools boy

**WPA3 (Enterprise Mode) - AES 192 bits and SHA-384 , Personal CCMP 128-bit**\
**Removed pre-shared key exchange**

Simultaneous Authentication of Equals(SAE) used

Perfect Forward Secrecy - Assures session key won't be compromised even if long-term secrets used in key exchange are

