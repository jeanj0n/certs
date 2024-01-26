# S3 Security

### Object Encryption

* Server-Side Encryption (SSE-S3, Default) – keys handled, managed, and owned by AWS
* Server-Side Encryption with KMS (SSE-KMS) – uses KMS to manage keys
* Server-Side Encryption with Customer-Provided Keys (SSE-C) - manage own encryption keys
* Client-Side Encryption

#### SSE-S3

AES-256, set header "x-amz-server-side-encryption": "AES256"

<div align="left">

<figure><img src="../../.gitbook/assets/image (81).png" alt="" width="375"><figcaption></figcaption></figure>

</div>

#### SSE-KMS

KMS - user control + audit key usage using CloudTrail\
set header value to KMS instead of AES256

When you upload it calls the GenerateDataKey KMS API download calls Decrypt API, max limit capped&#x20;

**SSE-C**

Only HTTPS allowed HTTP ok for above two, keys must always be mentioned in header

<div align="left">

<figure><img src="../../.gitbook/assets/image (82).png" alt="" width="375"><figcaption></figcaption></figure>

</div>

<div align="left">

<figure><img src="../../.gitbook/assets/image (83).png" alt="" width="563"><figcaption><p>Force Encryption in Transit aws:SecureTransport</p></figcaption></figure>

</div>

**Bucket Policies are evaluated before “Default Encryption”**

### CORS (Cross Origin Resource Sharing)

Origin = scheme (protocol) + host (domain) + port

* Same origin: http://example.com/app1 & http://example.com/app2
* Different origins: http://www.example.com & http://other.example.com

<div align="left">

<figure><img src="../../.gitbook/assets/image (84).png" alt="" width="563"><figcaption><p>Without setting the headers beforehand, cannot request content from another source will only get rejected</p></figcaption></figure>

</div>

### MFA Delete

MFA needed to permanently delete an object version and suspend versioning\
Not needed for enable versioning and list deleted versions

Versioning required to enable MFA Delete\
Only the bucket owner (root account) can enable/disable MFA Delete

### S3 Access Logs

Any request made to S3, from any account, authorized or denied, will be logged into another S3 bucket, use Athena to perform data analysis on this separate bucket

<div align="left">

<figure><img src="../../.gitbook/assets/image (85).png" alt="" width="339"><figcaption><p>don't put app and log bucket in same ples</p></figcaption></figure>

</div>

#### S3 Glacier Vault Lock

Adapt WORM model, Helpful for compliance and data retention

### S3 Object Lock

<div align="left">

<figure><img src="../../.gitbook/assets/image (86).png" alt="" width="563"><figcaption></figcaption></figure>

</div>

<div align="left">

<figure><img src="../../.gitbook/assets/image (87).png" alt="" width="563"><figcaption></figcaption></figure>

</div>

<div align="left">

<figure><img src="../../.gitbook/assets/image (90).png" alt="" width="375"><figcaption><p>Access Points – VPC Origin</p></figcaption></figure>

</div>

<div align="left">

<figure><img src="../../.gitbook/assets/image (89).png" alt="" width="563"><figcaption></figcaption></figure>

</div>
