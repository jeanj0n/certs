# Breaching AD

We need OWASP ZAP and wappalyzer cus that shi helpred check the forum on Hydra

If you are using a Kali VM, Network Manager is most likely used as DNS manager. You can use GUI Menu to configure DNS:

* Network Manager -> Advanced Network Configuration -> Your Connection -> IPv4 Settings
* Set your DNS IP here to the IP for THMDC in the network diagram above<br>
* Add another DNS such as 1.1.1.1 or similar to ensure you still have internet access
* Run `sudo systemctl restart NetworkManager` and test your DNS similar to the steps above.

Why is this important you ask?

Kerberos, relies on DNS to create tickets. Tickets cannot be associated with IPs, so DNS is a must. If you are going to test AD networks on a security assessment, you will have to equip yourself with the skills required to solve DNS issues.

### NTLM Authenticated Services

{% embed url="https://security.stackexchange.com/questions/273649/using-hydra-on-a-ntlm-authentication" %}

<figure><img src="../../.gitbook/assets/image (65).png" alt=""><figcaption></figcaption></figure>

### LDAP Bind Credentials

Used by:

* Gitlab
* Jenkins
* Custom-developed web applications
* Printers
* VPNs

These guys have their own AD credentials and when you try to use these services, they contact the DC to create a bind request and then get back to you. So if you crack the credentials of this printer/whatever you can forge your requests.&#x20;

We host a rogue server, where we downgrade so authentication is sent via plaintext, when use regular `nc -389` it try to use secure auth. To do this, make a new file

```
#olcSaslSecProps.ldif
dn: cn=config
replace: olcSaslSecProps
olcSaslSecProps: noanonymous,minssf=0,passcred

sudo ldapmodify -Y EXTERNAL -H ldapi:// -f ./olcSaslSecProps.ldif && sudo service slapd restart
```

Setup slapd, refer room for setup. Use tcpdump to capture the traffic when exchange happens and voila.

<figure><img src="../../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

### Authentication Relay

Basically a MitM attack when user trying to legit auth w traget server. We can either just capture and do our own thing or relay this traffic (lil more difficult). **Responder** can be used to capture these auth requests on the network

Responder will attempt to poison any Link-Local Multicast Name Resolution (LLMNR), NetBIOS Name Service (NBT-NS), and Web Proxy Auto-Discovery (WPAD) requests that are detected.

Although it can intercept and poison requests from our box, don't because normal attempts could fail meaning there could be a disruption and users and services can't connect as intended.

<figure><img src="../../.gitbook/assets/image (88).png" alt=""><figcaption><p>thm boys send out a auth request every 30 min for us to intercept</p></figcaption></figure>

### Microsoft Deployment Toolkit (MDT)

PXE (Preboot Execution Environment) is used for new systems to install OS over a network&#x20;

Once DHCP assign an IP to user, TFTP is used to communicate with MDT. Use this to download the PXE boot image

TFTP doesn't allow file listing so gotta be careful.

Get it and then use PowerPXE on Powershell (like PowerUp.ps1) to read the contents , no ss :(

### Configuration Files

Assume we got our hands on a database, apparently McAffee hardcodes credentials used during installation

<figure><img src="../../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>

There is a GitHub which deals w decryption from mcaffee db, use that w the password you obtained
