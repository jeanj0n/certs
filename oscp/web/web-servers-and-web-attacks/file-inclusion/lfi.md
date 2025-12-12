# LFI

* System is accessing a local resource
* User input is being downloaded

## LFI Hitlist

* etc/passwd -> user SSH keys in `home/[user]/.ssh/id_rsa`
* apache webroot -> gotta know where stuff is stored (check the previous page)
* webapp creds
* service creds (could be of another port eg. redis or whatever)
* `.bash_history or .zsh_history` of users who have most odds of running the app (find location of stuff)
* `/proc/self/cmdline`  \
  `/proc/self/environ`  \
  `/proc/self/status`  \
  `/proc/version`

{% embed url="https://github.com/RoqueNight/LFI---RCE-Cheat-Sheet" %}

{% embed url="https://sushant747.gitbooks.io/total-oscp-guide/content/local_file_inclusion.html" %}
Sensitive file list
{% endembed %}
