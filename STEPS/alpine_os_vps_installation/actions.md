
### 1. Download alpine OS  and check

Alpine images are available on their download page [https://alpinelinux.org/downloads/](https://alpinelinux.org/downloads/)

From those, you have to chose the one that best suits your needs. For reference, in this example, we will be running alpine on an x86_64 architecture VPS. Therefore, we will use the "Virtual" image, optimized for virtual systems.  

We download the selected image, as well as its sha256sum and gpg files, for integrity and authenticity checks



```
cd /
sudo wget https://dl-cdn.alpinelinux.org/alpine/v3.20/releases/x86_64/alpine-virt-3.20.1-x86_64.iso 
sudo wget https://dl-cdn.alpinelinux.org/alpine/v3.20/releases/x86_64/alpine-virt-3.20.1-x86_64.iso.sha256 
sudo wget https://dl-cdn.alpinelinux.org/alpine/v3.20/releases/x86_64/alpine-virt-3.20.1-x86_64.iso.asc
```

Integrity and Authenticity checks :

```
#Integrity check 
sha256sum -c alpine-virt-3.20.1-x86_64.iso.sha256

# Authenticity check
sudo wget https://alpinelinux.org/keys/ncopa.asc
gpg --import ncopa.asc 
gpg --verify alpine-virt-3.20.1-x86_64.iso.asc alpine-virt-3.20.1-x86_64.iso
```
We now have the alpine image avilable and checked.

### 2. Prepare access to the GRUB menu 

Most VPS users want their servers to reboot promptly when it needs to happen. Therefore, cloud providers often set the GRUB menu timeout option to 0, which prevents GRUB menu display upon startup.

As we will need to access it, once logged into the VPS, navigate to the `/etc/default` folder.
There will be a `grub` file and a `grub.d/` folder ( grub.d files supersede the /etc/default/grub file instructions). Make sure that you set ```GRUB_TIMEOUT=5 ``` or more.
For this modification to be effective, the grub configuration file needs to be updated :

```
#Alternatively use this command
echo "GRUB_TIMEOUT=5" | sudo tee /etc/default/grub.d/timeout.cfg

sudo update-grub
```


### 3. Boot into alpine live system

>WARNING :<br>
<br>
From the moment we reboot the system, to perform grub menu entries, until we finish the alpine os initial setup, the SSH connection will not be available.
>

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

`grub> boot`


### 4. Post-Boot File Manipulation and Mounting:

The grub configuration we have setup, boots an alpine live environment and prompt us with an initial shell. 

This environment is not persistent, the changes we make will be lost upon next reboot.
Nevertheless this environment provides the required tools to manage mounts and make installation files (contained in the iso) available at the right location.
Into this initial prompted shell :

1. Mount the iso containing partition, to access its files
```mount /dev/sda1 /media/sda1```

2. Copy the ISO file from that partition, to a temporary RAM-based filesystem
```cp /media/sda1/alpine-virt-3.14.2-x86_64.iso /dev/shm```

3. Unmount the host partition
```umount /dev/sda1```

4. Mount the ISO file as if it were a CD-ROM to access its contents
```mount -o loop -t iso9660 /dev/shm/alpine-virt-3.14.2-x86_64.iso /media/cdrom```





### 5. Alpine installation




[Back to Overview](overview.md)