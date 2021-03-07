## Configure network
Begin with simple commands.

```
# yum install net-tools  
# ip addr show
```

Now open and edit file /etc/sysconfig/network-scripts/ifcfg-enp0s3 using your choice of editor. Here, I’m using Vi editor and make sure you must be root user to make changes…

```
# vi /etc/sysconfig/network-scripts/ifcfg-enp0s3
```

Now we will be editing four fields in the file. Note the below four fields and leave everything else untouched. Also leave double quotes as it is and enter your data in between.

```
IPADDR = “[Enter your static IP here]” 
GATEWAY = “[Enter your Default Gateway]”
DNS1 = “[Your Domain Name System 1]”
DNS2 = “[Your Domain Name System 2]”
```

After making the changes ‘ifcfg-enp0s3‘, notice your IP, GATEWAY and DNS will vary. Save and Exit.
Restart service network and check the IP is correct or not, that was assigned. If everything is ok, Ping to see network status…

```
# service network restart
```

After restarting network, make sure to check the IP address and network status…

```
# ip addr show
# ping -c4 google.com
```

## Update CentOS 7

```
# yum -y update && yum -y upgrade
```

## Install very useful tools 

```
# yum install links wget git p7zip net-tools bind-utils epel-release p7zip-plugins fdupes screen 
```

## If you need a Desktop environment (OPTIONAL)

```
yum group install "GNOME Desktop" "Graphical Adminitration Tools"
```
```
ln -sf /lib/systemd/system/runlevel5.target
/etc/systemd/system/default.target
reboot
```

## User Management
https://www.digitalocean.com/community/tutorials/how-to-create-a-sudo-user-on-centos-quickstart

User operations on the username anotheruser:

```
adduser formation
passwd formation
# Grant sudo (OPTIONAL)
usermod -aG wheel username
# By default, on CentOS, members of the wheel group have sudo privileges.
```

## Install and Configure SSH Server
SSH stands for Secure Shell which is the default protocol in Linux for remote management. SSH is one of those essential piece of software which comes default with CentOS Minimal Server.

Check Currently Installed SSH version.

```
# SSH -V
```

Use Secure Protocol over the default SSH Protocol and change port number also for extra Security. Edit the SSH configuration file ‘/etc/ssh/sshd_config‘.
Uncomment the line below line or delete 1 from the Protocol string, so the line seems like:

```
# Protocol 2,1 (Original)
Protocol 2 (Now)
```
This change force SSH to use Protocol 2 which is considered to be more secure than Protocol 1 and also make sure to change the port number 22 to any in the configuration.

Disable SSH ‘root login‘ and allow to connect to root only after login to normal user account for added additional Security. For this, open and edit configuration file ‘/etc/ssh/sshd_config‘ and change PermitRootLogin yes t PermitRootLogin no.

```
# PermitRootLogin yes (Original) 
PermitRootLogin no (Now)
```

Finally, restart SSH service to reflect new changes..

```
# systemctl restart sshd.service
```

## Enable Third Party Repositories
It is not a good idea to add untrusted repositories specially in production and it may be fatal. 
However just for example here we will be adding a few community approved trusted repositories to install third party tools and packages.

Add Extra Package for Enterprise Linux (EPEL) Repository.

```
# yum install epel-release
```

Add Community Enterprise Linux Repository.

```
# rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm
```

## Install & configure `sudo` + Superpower rights for current user

Give user a privilege to switch to root as administator.

```
usermod -G wheel "formation"
vim  /etc/pam.d/su
```

Uncomment line 6 to look like one shown below.

```
auth            required        pam_wheel.so use_uid
```

Transfer root privilege to a user you added, here the username is “formation”.


`sudo` which is commonly called as super do as well as suitable user do is a program for UNIX-like operating system to execute a program with the security privileged of another user. Let’s see how to configure sudo…

```
# visudo
```
It will open the file /etc/sudoers for editing..
Give all the permission (equal to root) to a user (say tecmint), that has already been created.

```
formation   ALL=(ALL)    ALL
```
Give all the permission (equal to root) to a user (say formation), except the permission to reboot and shutdown the server.

Again open the same file and edit it with the below contents.

```
cmnd_Alias nopermit = /sbin/shutdown, /sbin/reboot
```
Then add alias with Logical (!) operator.

```
formation   ALL=(ALL)    ALL,!nopermit
```

## Disable SELinux (Security-Enhanced Linux) if you don’t need it.

```
sed -i 's/(^SELINUX=).*/SELINUX=disabled/' /etc/selinux/config
```

If you reboot your system and type

```
sestatus
```
You should get the output saying selinux have been disabled. See below

```
SELinux status:                 disabled
```