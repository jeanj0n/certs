# Extra Stuff (Acronyms and Diffs)

{% embed url="https://cybr.notion.site/CompTIA-Security-Acronyms-51a0f80070994da5b524bc4e6be13008" %}
Sauce🌶️
{% endembed %}

Check Wireless Security from Net+ pls

* RTO is an expectation (may), MTTR is facts - MTTR should always be less than RTO
* IoC (Indicators of Compromise) - Forensic data found in systems via log entries or files that identify potentially malicious activity on a system or network
* CBC - Cipher block chaining
* CERT/CSIRT - Response Team (Mainly for IR)
* CSP (Cloud Service Provider)
* DPO (Data Privacy Officer)
* ICS (Industrial Control Systems) - SCADA low scale
* LEAP (EAP proprietary by Cisco)
* PEAP - version of EAP uses AD and server certificates
* SOAP (Simple Object Access Protocol) - Lightweight XML-based protocol that's used for exchanging information in decentralized, distributed application environments
* PUP (Potentially Unwanted Program)
* SPIM (Spam over Internet Messaging) - Social Engineering over text
* SRTP (Secure Real-Time Protocol) - Provides encryption, message authentication and integrity, and replay attack protection to the RTP data

## E-mail Security

* SFP -  find out whether a mail server is authorized to deliver email for a sending domain in DNS. prevent spammers from sending messages on behalf of your domain
* DKIM - signatures are applied by the mail server of the senders domain.
* DMARC - response to receiving an email that fails to authenticate using SPF or DKIM
* SMIME - signature **and encryption** is applied by the sender (end user)

## Attack Definitions

* Rainbow attack - A rainbow table is a precomputed table for caching the outputs of a cryptographic hash function, usually for cracking password hashes
* Birthday attack - banking on the odds of two diff entities producing same hash
* Cognitive Password - form of knowledge-based authentication that requires a user to answer a question, presumably something they intrinsically know, to verify their identity
* War walking - Surveying the external of a building to check for open wifi, you mark the area
* Credentialed scan -  involves the use of privileged credentials to scan systems and applications. This type of scanning provides an in-depth and comprehensive analysis of vulnerabilities and provides more accurate results.\
  \
  Internal scan - An internal scan is conducted within a company's network to ensure safety inside of the network, can't figure out the system configs of a workstation with this

<div align="left"><figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure></div>

## Differentiate

OpenID and OAuth the tag team, authenticate and authorize respectively and both use ID tokens.\
SAML uses XML and uses assertions, explicitly authenticate and implicitly authorize using tags.

<div align="left"><figure><img src="../../.gitbook/assets/image (72).png" alt="" width="375"><figcaption></figcaption></figure></div>

Anomaly vs Behaviour - anomaly define expected outcome and anything outside it is flagged, behaviour is expected events associated w a user and anything that deviates flagged

<div align="left"><figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="500"><figcaption></figcaption></figure></div>

EPP **Signature** (Endpoint protection platform) vs EDR **Anomaly** (Endpoint detection and response) - Refer Sec applications & devices last section ples

<div align="left"><figure><img src="../../.gitbook/assets/image (65).png" alt="" width="563"><figcaption></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (66).png" alt="" width="563"><figcaption></figcaption></figure></div>

**Jumpbox vs Bastion Host**&#x20;

<div align="left"><figure><img src="../../.gitbook/assets/image (26).png" alt="" width="563"><figcaption></figcaption></figure></div>
