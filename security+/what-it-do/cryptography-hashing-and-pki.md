# Cryptography, Hashing and PKI

## Cryptography

### Based on key type

* **Symmetric :** Private key\
  DES - 64-bit blocks but effective key strength of 56-bits\
  3DES - thrice the power of DES \
  IDEA - 64-bit blocks, little more effective than DES\
  AES  - 128-bit, 192-bit, or 256-bit blocks, standard for US govt\
  Blowfish - uses 64-bit blocks and a variable length encryption key\
  Twofish - uses 128-bit blocks and a 128-bit, 192-bit, or 256-bit encryption key\
  RC4 - uses a variable key size from 40-bits to 2048-bits that is used in SSL and WEP
* Asymmetric - Public\
  Diffie Hellman - used for key exchange\
  RSA - 1024 to 4096 bits\
  ECC - 256-bit key is just as secure as RSA with a 2048-bit key

**Tip - RC4 is the only stream scheme covered, rest are block schemes**

### Based on math algorithm used

* Stream - Utilizes a keystream generator to encrypt data bit by bit using a mathematical XOR function to create the ciphertext
* Block - Breaks the input into fixed-length blocks of data and performs the encryption on each block, easier to implement by software

**Pretty Good Privacy (PGP)**

Encryption program used for signing, encrypting, and decrypting emails, uses IDEA encryption.

Symmetric functions - 128-bit or higher keys, asymmetric functions - 512-bit to 2048-bit key sizes

**GPG (GNU Privacy Guard)** - Newer version, cross platform, uses AES

**Ephemeral** - Key generated for each execution of a key establishment process, short-lived and used in WPA3 to create perfect forward secrecy

## Hashing

* MD5 - 128 bit
* SHA-1 - 160 bit&#x20;
* SHA-2 - 224, 256, 348 and 512 bits
* SHA-3 - between 224 and 512 bits, newer than SHA-2&#x20;
* RACE Integrity Primitive Evaluation Message Digest(RIPEMD) - open source, 160, 256 or 320 bit
* Hash-based Message Authentication Code (HMAC) - uses hashing to provide integrity and authenticity eg. HMAC-MD5, HMAC-SHA1, HMAC-SHA256
* LANMAN (LM Hash) - Used by Windows, uses DES and limited to 14 characters split to two blocks of 7
* NT LAN Manager Hash (NTLM Hash) - Uses RC4
* NTLMv2 Hash - Uses HMAC-MD5, used when you do not have a domain with Kerberos for authentication

#### Hashing Attacks

* Pass the hash - Pass direct hash value against server without cracking plaintext value,           eg. **Mimikatz**
* Birthday attack - Trying to create hash collision, 99% chance in 57 people group, 50% in 23

#### Increasing Hash Security

* Key Stretching - Repeat the hashing process numerous times to reduce the number of collisions possible, hence making it harder to crack\
  Eg. WPA, WPA2, PGP, bcrypt
* Salting - Adding random data in a one-way crypto hash, “nonce” is used to prevent password reuse

## Public Key Infrastructure

An entire system of hardware, software, policies, procedures, and people that is based on asymmetric encryption

### Certificates

* X.509 - Standard used PKI for digital certificates, has owner info and CA info
* Wildcard Certificates - Allow all subdomain to use same certificate, easier to manage
* Subject Alternative Name (SAN) - Allow owner to specify additional domains and IPs to be supported

X.690 uses BER, CER, and DER for encoding

* BER (Basic Encoding Rules) - Original ruleset governing encoding data structure, can use several coding schemes
* CER (Canonical Encoding Rules) - Restricted BER, uses only one encoding type
* DER (Distinguished Encoding Rules) - More restrictive rules for length, character strings, and how elements of a digital certificate are stored in X.509

<div align="left"><figure><img src="../../.gitbook/assets/image (149).png" alt="" width="262"><figcaption></figcaption></figure></div>

### Certificate Authorities

* Registration Authority - verify user info before issuing the certificate
* CSR (certificate signing request) - Submitted to the CA to request a digital certificate
* Certificate Revocation List (CRL) - List of certificates CA has revoked
* Online Certificate Status Protocol (OCSP) - Shows revocation status of a cert using serial number
* Public Key Pinning - Prevent HTTPS websites to resist impersonation by presenting trusted keys to users web browser in its HTTP header
* Key Escrow - Copy of a user’s private key is held in case the user accidently loses their key
* Key Recovery Agent - Allows restoration of lost or corrupt key

#### Web of Trust

A decentralized trust model that addresses issues associated with the public authentication of public keys within a CA-based PKI system

A peer-to-peer model

PGP is a web of trust
