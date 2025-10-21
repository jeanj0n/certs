# File Upload

We already know

* Null byte poisoning
* Using PHP payloads in an image file (vim)
* Playing around with extensions and encoding

## Lab 3 (Directory Traversal)

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

In the first two labs, no file extension checking, here only raw content of the files uploaded are being displayed. You can upload the php webshell but can't execute commands.

However, there maybe a rule that only executes this filter on files uploaded at this specific directory 'avatars'. If we try to escape this then we might be able to execute

`../` was filtered so URL encoding seems to do the trick. Reading the file is as usual

## Lab 4 (Blacklist filter)

{% embed url="https://www.jamesparker.dev/can-i-create-multiple-htaccess-files-for-different-directories/" %}

The fact that different `.htaccess` files can be used for specific directories and their corresponding control is insane.

We can only do that because the response mentions the use of Apache.

Add a `.htaccess` file adding the type 'application/x-httpd-php' basically executable PHP and mentioning our own extension, we bypass the blacklist now.

Why did URL encoding and directory traversal not work or show not found? The response showed it was successful. php5 also only downloaded the uploaded file instead of any execution

<div align="left"><figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt="" width="503"><figcaption></figcaption></figure></div>

<div align="left"><figure><img src="../../.gitbook/assets/image (7) (1).png" alt="" width="504"><figcaption></figcaption></figure></div>

