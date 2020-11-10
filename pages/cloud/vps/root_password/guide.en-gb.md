---
title: Changing your root password on a VPS
slug: root-password
excerpt: Find out how to change the root password of a VPS
section: Diagnostics and rescue mode
---

**Last updated 10th November 2020**

## Objective

It may become necessary to change the root password on your Linux operating system. There are two possible scenarios:

- You are still able to log in via SSH
- You are unable to log in via SSH because you have lost your root password

**This guide will explain how to proceed with changing your root password depending on the initial situation.**

## Requirements

- an OVHcloud [VPS service](https://www.ovhcloud.com/en-gb/vps/) already set up
- login credentials received via email after the installation (if still valid)
- access to the [OVHcloud Control Panel](https://www.ovh.com/auth/?action=gotomanager) (for using rescue mode)

> [!warning]
>OVHcloud is providing you with services for which you are responsible, with regard to their configuration and management. You are therefore responsible for ensuring they function correctly.
>
>This guide is designed to assist you in common tasks as much as possible. Nevertheless, we recommend contacting a specialised provider and/or the software publisher for the service if you encounter any difficulties. We will not be able to assist you ourselves. You can find more information in the “Go further” section of this guide.

## Instructions

### Changing the password if you still have access (sudo user or root)

> [!primary]
>
> For more information about connecting to your VPS, please consult the [first steps guide](../getting-started-vps).
>

Log in to your VPS via SSH. Switch to the root user, if necessary:

```sh
~$ sudo su -
~#
```

Change the password:

```sh
~# passwd
New password:
Retype new password:
passwd: password updated successfully
```

> [!primary]
>
> On a Linux distribution, the password you enter **will not appear**.
>

If you need to permit logging in as root, follow the steps in [this guide section](./#enabling-root-login).

### Changing the password if you have lost it

<iframe width="560" height="315" src="https://www.youtube.com/embed/ua1qoTMq35g?rel=0" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

#### Step 1: Restart the VPS into rescue mode

Log in to your [OVHcloud Control Panel](https://www.ovh.com/auth/?action=gotomanager) and reboot the VPS in rescue mode. If you need further instructions about using rescue mode with a VPS, you may consult the [rescue mode guide](../rescue/).

#### Step 2: Identify the mount point

The mount is created automatically. Use the following commands to identify where your partition is mounted:

##### **df -h**

```sh
df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            5.8G     0  5.8G   0% /dev
tmpfs           1.2G   17M  1.2G   2% /run
/dev/sda1       2.4G  1.5G  788M  66% /
tmpfs           5.8G     0  5.8G   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           5.8G     0  5.8G   0% /sys/fs/cgroup
/dev/sdb1        49G  1.2G   48G   3% /mnt/sdb1
/dev/sdb15      105M  3.6M  101M   4% /mnt/sdb15
```

##### **lsblk**

```sh
lsblk
NAME    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda       8:0    0  2.5G  0 disk
└─sda1    8:1    0  2.5G  0 part /
sdb       8:16   0   50G  0 disk
├─sdb1    8:17   0 49.9G  0 part /mnt/sdb1
├─sdb14   8:30   0    4M  0 part
└─sdb15   8:31   0  106M  0 part /mnt/sdb15
```

The example output above shows that the system partition is mounted on **/mnt/sdb1**.

#### Step 3: CHROOT permissions

You now need to edit the root directory to apply the changes to your system. You can do this by using the `chroot` command:

```sh
chroot /mnt/sdb1/
```

You can check by typing the `ls -l` command, which will list the content stored in the current directory of your system:

```sh
ls -l
```

#### Step 4: Change the root password

In the last step, change the root password with the `passwd` command:

```sh
~# passwd
New password:
Retype new password:
passwd: password updated successfully
```

Finally, reboot your VPS on its drive in your [OVHcloud Control Panel](https://www.ovh.com/auth/?action=gotomanager).


### Enabling root login

If your VPS is of the current ranges (naming scheme: *vps-XXXXXXX.vps.ovh.net*), you have received login credentials for a user with elevated permissions instead of the default "root" account. Additionally, the SSH service is not accepting login requests as root.

> [!warning]
>
> Enabling root logins is usually regarded as a security vulnerability and is therefore not recommended.
>
> We recommend taking measures to secure your VPS first. You can refer to our guide on [Securing a VPS](../tips-for-securing-a-vps/).
>

#### Step 1: Edit the sshd_config file

Use a text editor such as vim or nano to edit this configuration file:

```sh
nano /etc/ssh/sshd_config
```

Add or edit the following line.

```sh
PermitRootLogin yes
```

Save the file and exit the editor.

#### Step 2: Restart the SSH service

```sh
systemctl restart ssh
```

This should be sufficient to apply the changes. Alternatively, reboot the VPS (```~$ sudo reboot```).

### Troubleshooting

If you encounter boot issues after you have changed your password and initiated the reboot:

- Check the KVM for important information as to why the VPS cannot start. Consult the [KVM guide](../use-kvm-for-vps/) for help using this feature in the OVHcloud Control Panel.
- If the KVM is showing the VPS booting or unable to find the disk, ensure you have [boot logs enabled](../use-kvm-for-vps/). Relay the pertinent logs to our  support teams by creating a support request in your [OVHcloud Control Panel](https://www.ovh.com/manager/dedicated/#/support/tickets/new) for further investigations.

## Go further

[Introduction to SSH](../../dedicated/ssh-introduction/)

[Securing a VPS](../tips-for-securing-a-vps/)

Join our user community on <https://community.ovh.com/en/>.
