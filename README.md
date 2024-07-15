# ALPINE VPS MANAGER

Alpine Linux is a security-oriented, lightweight Linux distribution.
<br>
By following this guide, you will set up a secure and efficient Alpine Linux environment on your VPS.

- Installation of alpine base image on the VPS
- Firewall basic rules setup
- SSH port change
- Docker install

<br>

> WARNING :\
This guide assumes a basic level of familiarity with Linux command-line operations.\
Proceed with caution and ensure that you have backups of any important data before making significant changes to your VPS configuration.
**The Alpine installation will completely overwrite the current OS and all existing files.**\
Improper handling of some steps, particularly those involving bootloader and partition changes, can render your VPS inoperable.\
**GRUB bootloader and console access required**
>

*Cloud providers console access : <br>
AWS: EC2 Instance Connect, DigitalOcean : Droplet Console, Linode : Lish, OVH : KVM,
Vultr : View Console, GCP and Azure : Serial Console*

<br>

## Alpine base image install on VPS

In order to install the alpine image on the remote VPS (debian 12 host in this example), we will need to :

### 1. Prepare access to GRUB menu 

Most VPS users want their servers to reboot promptly when it needs to happen. Therefore, cloud providers often set the GRUB menu timeout option to 0, which prevents GRUB menu display upon startup.

As we will need to access it, once logged into the VPS, navigate to the `/etc/default` folder.
There will be a `grub` file and a `grub.d/` folder ( grub.d files supersede the /etc/default/grub file instructions). Make sure that you set ```GRUB_TIMEOUT=5 ``` or more.
For this modification to be effective, the grub configuration file needs to be updated :

```
#Alternatively use this command
echo "GRUB_TIMEOUT=5" | sudo tee /etc/default/grub.d/timeout.cfg

sudo update-grub
```


### 2. Download alpine OS  and check

Alpine images are available on their download page [https://alpinelinux.org/downloads/](https://alpinelinux.org/downloads/)

As we will be running VPS hosted, based on x86_64 architecture, we select the appropriate virtual x86_64 image and download the sha256sum and gpg files, for integrity and authenticity checks

```
cd /
sudo wget https://dl-cdn.alpinelinux.org/alpine/v3.20/releases/x86_64/alpine-virt-3.20.1-x86_64.iso 
sudo wget https://dl-cdn.alpinelinux.org/alpine/v3.20/releases/x86_64/alpine-virt-3.20.1-x86_64.iso.sha256 
sudo wget https://dl-cdn.alpinelinux.org/alpine/v3.20/releases/x86_64/alpine-virt-3.20.1-x86_64.iso.asc
```

Integrity check and Authenticity check *(optional but recommended)*:

```
#Integrity check 
sha256sum -c alpine-virt-3.20.1-x86_64.iso.sha256

# Authenticity check
sudo wget https://alpinelinux.org/keys/ncopa.asc
gpg --import ncopa.asc 
gpg --verify alpine-virt-3.20.1-x86_64.iso.asc alpine-virt-3.20.1-x86_64.iso
```
Now that we have the alpine image avilable and checked, we will boot from it. 


### 3. Boot from alpine


From the moment we reboot the systsem, to perform grub menu entries, until we finish the alpine os initial setup, the SSH connection will not be available.

For further action, enter your cloud provider console access *(see list above)*.

Inside the console, reboot your system and upon startup, when grub options appear, press 'c' to enter grub menu.
You should see the prompt :\
`grub>`

In the GRUB bootloader, we assign the alpine ISO as a loopback device, so the system treats it as a virtual disk it can boot from

```grub> loopback loop /alpine-virt-3.14.2-x86_64.iso```

To make it bootable, we need to load the kernel and the initial ramdisk from the loopback device (loop)

```
grub> linux (loop)/boot/vmlinuz-virt
grub> initrd (loop)/boot/initramfs-virt
```

We can then boot into the live alpine system

```
grub> boot
```


### 4. Post-Boot File Manipulation and Mounting:


