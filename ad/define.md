# Define

<div align="left"><figure><img src="../.gitbook/assets/image (46).png" alt=""><figcaption></figcaption></figure></div>

<div align="left"><figure><img src="../.gitbook/assets/image (39).png" alt="" width="563"><figcaption><p>Kerboros Working</p></figcaption></figure></div>

<div align="left"><figure><img src="../.gitbook/assets/image (40).png" alt="" width="563"><figcaption><p>NTLM Working</p></figcaption></figure></div>

<figure><img src="../.gitbook/assets/image (79).png" alt=""><figcaption><p>LDAP Bind</p></figcaption></figure>

AD - Centralise the administration of common components of Windows network in a single repo \
Domain Controller (DC) - the server that runs AD, contain hashed passwords for all user accounts within the environment.

IAM and security policies can be implemented on a central interface now.\
Implement policies to restrict admin privileges universally across the domain

### Active Domain Directory Service (AD DS)

Has info of all 'objects' of our network like users, groups, machines, printers etc.

**Users** - known as **security principals(SP)**, they can be auth and have privs over resources like printers etc. There are two types of users

* People: represent persons in your organisation that need to access the network, like employees.
* Services: only have the privileges needed to run their specific service eg. MySQL

**Machines** - for every computer in the domain, a machine object is created considered SP \
passwords are auto rotated and consist of 120 random characters.

A machine account will be named after its own name followed by $  eg.TM01$

### Security Groups

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

**Organizational Units (OUs)**  are container objects that allow you to classify users and machines. Define sets of users with similar policing requirements. One user can only be in one OU

Default containers (OU) created by Windows

<div align="left"><figure><img src="../.gitbook/assets/image (33).png" alt="" width="563"><figcaption></figcaption></figure></div>

#### OU vs Security Groups

OU is used to implement policies on users and computers, specific configs to user depending on their role in organization

Security Groups grant perms over resources (users access shared folder), user can be part of multiple groups

<div align="left"><figure><img src="../.gitbook/assets/image (34).png" alt="" width="563"><figcaption></figcaption></figure></div>

OU are accidental-delete protected by default, uncheck that under Advamced features to delete OUs for management purposes.

**Delegation** - allows you to grant users specific privileges to perform advanced tasks on OUs without needing a Domain Administrator to step in.\
Instead of vertical hierarchy think of it as giving the power to someone else eg. you delegate a control on sales OU to Philip from IT support to reset passwords similarly do same for other departments.

Delegating a power doesn't mean they can perform said operation via **Active Directory Users and Computers**

### Login via RDP

xfreerdp /u:THM\phillip /p:Claire2008 /v:10.10.225.26 /dynamic-resolution

Username format : THM\philip \[even tho in the login it shows \<room name>\\\<username>, fix it]

<div align="left"><figure><img src="../.gitbook/assets/image (84).png" alt="" width="563"><figcaption><p>Create a new OU under Domain Container (thm.local)</p></figcaption></figure></div>

To delete OU's and prevent accidental delete

<div align="left"><figure><img src="../.gitbook/assets/image (47).png" alt="" width="375"><figcaption></figcaption></figure></div>

<div align="left"><figure><img src="../.gitbook/assets/image (48).png" alt="" width="246"><figcaption></figcaption></figure></div>

```
Set-ADAccountPassword sophie -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password') -Verbose
```

#### Segregate devices into

* Workstations
* Servers
* Domain Controllers

### Group Policies

**Group Policy Management** - tool at start menu

implement via Group Policy Objects (GPO) - collection of settings that can be applied to OUs, can be applied either users or computers

Check out the different policy options that can be edited under various categories (Windows, Admin, Security)

GPOs are distributed to the network via a network share called `SYSVOL`, which is stored in the DC. SYSVOL share points by default to the `C:\Windows\SYSVOL\sysvol\` directory on each of the DCs in our network.

To force any computer to sync its GPO immediately instead of the usual 2 hours it takes to catch up

```shell-session
gpupdate /force
```

Once you make the GPO, drag it to the desired OU you wish to implement it on.

### Trees and Forest

<div align="left"><figure><img src="../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure></div>

**Two-way trust relationships** can also be made to allow both domains to mutually authorise users from the other. One-way is also fine.&#x20;
