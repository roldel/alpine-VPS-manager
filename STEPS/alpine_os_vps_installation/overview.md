## Alpine os VPS installation

### Overview

In order to install alpine os on a remote VPS, we will first need to download and check, an alpine os image.

Then we will setup the GRUB bootloader, in order to boot from the iso image, into an alpine live environement (operating from the RAM).<br>


This live environement is operational, but it is not persistent and lacks installation files and features, required for the full install of the alpine os.

Those missing elements are contained in the iso file. To make them available in our live system, we mount the main disk and copy the iso file from it, into the RAM.

We will then mount the RAM located iso, as a loopback device. This way the system will consider the iso as physical device based.
From here, the process becomes similar to a usb or cdrom based installation.

As both the live environment and the iso are then loaded into the RAM, the main disk is not in use anymore, can be unmounted and is free for overwritting, when running the alpine full installation setup. 


> WARNING :
proceeding beyond step 2, the SSH connection will not be possible until finalization of step 5. Console access is required to perform intermediate steps

>

Procedure [alpine_os_vps_installation/actions](actions.md)

[Project Root](../README.md)