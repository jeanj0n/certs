# IAM

## Policies

Define the permissions given to a user directly or group, a user can be part of multiple groups

<div align="left"><figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="442"><figcaption></figcaption></figure></div>

Version (always the same) and ID are part of the policy structure itself, the rest fall under statement

<div align="left"><figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="349"><figcaption></figcaption></figure></div>

### Means of Access

* AWS Management Console - password + MFA
* AWS CLI - access keys
* AWS SDK (for developing apps) - access keys

### Roles

Policies but for entities/services built-in by AWS (eg. EC2 is given Role to read user data)

### Security Tools

* IAM Credentials Report (account-level) - lists all your account's users and the status of their various credentials
* IAM Access Advisor (user-level) - shows the service permissions granted to a user and when those services were last accessed.
