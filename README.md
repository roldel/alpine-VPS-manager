# ALPINE VPS MANAGER
A step-by-step guide to setting up a low-overhead VPS server, focusing on security and efficiency

- Installation of alpine base image on the VPS
- Firewall basic rules setup
- SSH port change
- Docker install



## Alpine base image install on VPS

In order to install the base image on the remote VPS ( debian 12 host in this example ), we will need to :

### 1. Pepare access to GRUB menu 

Most VPS users want their servers to reboot quick when it needs to happen. Therefore, cloud providers will usually set the GRUB menu timeout to 0, which will prevent GRUB menu display.

As we will need to access it, once logged into the VPS, navigate to the /etc/default folder.
There will be a grub file and a grub.d folder ( grub.d files superseed the /etc/default/grub instructions ). Make sure that you set the ```GRUB_TIMEOUT=5 ``` or more.
For this modification to be effective, the grub configuration file needs to be updated :

```sudo update-grub```



### 2. Image Download and Integrity checks

