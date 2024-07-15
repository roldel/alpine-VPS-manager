# ALPINE VPS MANAGER

Alpine Linux is a security-oriented, lightweight Linux distribution. Its small size and minimalistic approach make it ideal for VPS, where efficiency and security are paramount.\
By following this guide, you will set up a secure and efficient Alpine Linux environment on your VPS.

- Installation of alpine base image on the VPS
- Firewall basic rules setup
- SSH port change
- Docker install


> WARNING :\
This guide assumes a basic level of familiarity with Linux command-line operations.\
Proceed with caution and ensure that you have backups of any important data before making significant changes to your VPS configuration.
The Alpine installation will completely overwrite the current OS and all existing files.\
Improper handling of some steps, particularly those involving bootloader and partition changes, can render your VPS inoperable.
>


## Alpine base image install on VPS

In order to install the base image on the remote VPS (debian 12 host in this example), we will need to :

### 1. Prepare access to GRUB menu 

Most VPS users want their servers to reboot promptly when it needs to happen. Therefore, cloud providers often set the GRUB menu timeout option to 0, which prevents GRUB menu display upon startup.

As we will need to access it, once logged into the VPS, navigate to the /etc/default folder.
There will be a grub file and a grub.d folder ( grub.d files supersede the /etc/default/grub file instructions ). Make sure that you set the ```GRUB_TIMEOUT=5 ``` or more.
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
