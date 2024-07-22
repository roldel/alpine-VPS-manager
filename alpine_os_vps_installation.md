## Alpine OS VPS installation

### Overview



To install Alpine OS on a remote VPS, complete the following steps:

1. Download and Verify Alpine Image

2. Prepare Access to GRUB menu
    - Check GRUB configuration, ensures GRUB menu display upon reboot

3. Setup Alpine Boot:
    - Configure GRUB to boot from the downloaded ISO image, which will attempt to launch an Alpine live environment running from RAM. **The initial attempt fails**

4. Post-Boot File Manipulation and Mounting:
    - The initial boot attempt fails because the system requires files from the ISO image, that are not automatically retrieved in this setup
    - Upon failure the initramfs emergency recovery shell is presented
    - Within the initramfs shell, manually load the ISO into RAM and make it available as a loopback device, simulating a plugged-in CD-ROM

5. Operational Live Environment and Full Installation:
    - The live environment is now operational. Though non-persistent, it comprises all the required files and tools to finalize the installation
    
    - Initiate the full installation by using the Alpine installer tool.


<br>

<hr>

<br>

### 1. Download and Verify Alpine Image

Alpine images are available on their download page [https://alpinelinux.org/downloads/](https://alpinelinux.org/downloads/)

From those, you have to chose the one that best suits your needs. In this example, we will run Alpine on an x86_64 architecture VPS. Therefore, we use the "Virtual" image, optimized for virtual systems.  

Download the selected image, as well as its sha256sum and gpg files, for integrity anS VPS installationd authenticity checks :



```
cd /
sudo wget https://dl-cdn.alpinelinux.org/alpine/v3.20/releases/x86_64/alpine-virt-3.20.1-x86_64.iso 
sudo wget https://dl-cdn.alpinelinux.org/alpine/v3.20/releases/x86_64/alpine-virt-3.20.1-x86_64.iso.sha256 
sudo wget https://dl-cdn.alpinelinux.org/alpine/v3.20/releases/x86_64/alpine-virt-3.20.1-x86_64.iso.asc
```

Integrity and Authenticity checks *(optional but recommended)* :

```
#Integrity check 
sha256sum -c alpine-virt-3.20.1-x86_64.iso.sha256

# Authenticity check
sudo wget https://alpinelinux.org/keys/ncopa.asc
gpg --import ncopa.asc 
gpg --verify alpine-virt-3.20.1-x86_64.iso.asc alpine-virt-3.20.1-x86_64.iso
```
The Alpine image is available and checked.

<br>

### 2. Prepare Access to GRUB menu

VPS users want their servers to reboot promptly. Therefore, cloud providers often set the GRUB menu timeout option to 0, which prevents GRUB menu display upon startup.

Check the `/etc/default` folder.\
It contains a `grub` file and a `grub.d/` folder ( `grub.d/` folder contained files supersede the `/etc/default/grub` file instructions).\
Make sure that you set ```GRUB_TIMEOUT=5``` or more.\
For this modification to be effective, the grub configuration file needs to be updated :

```
# Alternatively use this command
echo "GRUB_TIMEOUT=5" | sudo tee /etc/default/grub.d/timeout.cfg

# Update GRUB config
sudo update-grub
```

<br>

### 3. Set Up Alpine Boot

>WARNING :
From the moment we reboot the system, to perform grub menu entries, until we finalize the Alpine OS installation, the SSH connection will not be available.
>

Enter your cloud provider console access.

Inside the console, reboot the system and upon startup, when grub options appear, press 'c' to enter grub menu.
You should see the prompt :
```
grub>
```

In the GRUB bootloader, assign the Alpine ISO as a loopback device, so the system treats it as a virtual disk it can boot from

```
grub> loopback loop /alpine-virt-3.14.2-x86_64.iso
```

To make it bootable, load the kernel and the initial ramdisk from the loopback device (loop)

```
grub> linux (loop)/boot/vmlinuz-virt
grub> initrd (loop)/boot/initramfs-virt
```

Then boot into the live Alpine system

```
grub> boot
```

**The initial boot attempt fails**\
To boot successfully, the system requires files and tools from the ISO, mounted specifically. As it is not set up yet, it fails and prompts the "initramfs emergency recovery shell".\
Use this shell for further configuration.

<br>

### 4. Post-Boot File Manipulation and Mounting:

In the initramfs emergency recovery shell:
- mount the ISO image containing disk
- copy the ISO image into the RAM
- unmount the disk 
- mount the RAM loaded ISO image as a loopback device in /media/cdrom
<br>
<br>

```
# Mount the iso containing partition, to access its files
mount /dev/sda1 /media/sda1

# Copy the ISO file from that partition, to a temporary RAM-based filesystem
cp /media/sda1/alpine-virt-3.14.2-x86_64.iso /dev/shm

# Unmount the host partition
umount /dev/sda1

# Mount the ISO file as if it were a CD-ROM to access its contents
mount -o loop -t iso9660 /dev/shm/alpine-virt-3.14.2-x86_64.iso /media/cdrom
```


At this point :

- Alpine ISO kernel and initramfs have been assigned in the GRUB for boot
- Alpine ISO is mounted as a loopback device, and will be considered like a plugged-in physical cdrom
- Both above components are RAM loaded, rendering the system disks free for for the full installation overwritting 

<br>

Alpine live system is now ready for successful boot.

Exit the initramfs emergency shell and continue boot by typing 
```
exit
```

The Alpine welcome screen should display.

<br>

### 5. Operational Live Environment and Full Installation

When prompted for login, type `root`. The default created user named 'root', is intialized without password.

In Alpine welcome screen enter `setup-alpine` to initiate the Alpine installer.\
Follow up the question prompts.\
The default SSH behavior prohibit password login as root user. Along the installation process, create an additional user, ssh connection to it will be possible via password or provided ssh key.\
When it comes to disk, chose the disk to overwrite and choose the type 'sys'.

It is possible to automate this operation with an answefile, that will define the desired option for the installer configuration.

`setup-alpine -f <answerfile> `

<br>

**Security consideration :**<br>
<br>
As the Alpine installer process is managed from the cloud provider console, which may not be the most secure channel, it is advisable to initiate a secure SSH connection and change all passwords saved through the cloud provider console access, once the setup is complete.


<br>
<br>


Next step : [Alpine Firewall Setup](alpine_firewall_setup.md)

<br>

Back to project [README.md](README.md)