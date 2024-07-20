# ALPINE VPS MANAGER

Alpine Linux is a security-oriented, lightweight Linux distribution. Unfortunately, it does not always come as an option, amongst the default os offered by cloud providers.
<br>
By following this guide, you will set up a secure and efficient Alpine Linux environment on your VPS.

- Installation of alpine base image on the VPS [alpine_os_vps_installation](STEPS/alpine_os_vps_installation/overview.md)
- Firewall basic rules setup [alpine_firewall_rules_setup](STEPS/alpine_firewall_rules_setup/overview.md)
- SSH port change
- Docker install

<br>

> WARNING :\
<br>
This guide assumes a basic level of familiarity with Linux command-line operations.\
**The Alpine installation will completely overwrite the current OS and all existing files.**
Proceed with caution and ensure that you have backups of any important data before making significant changes to your VPS configuration.\
Improper handling of some steps, particularly those involving bootloader and partition changes, can render your VPS inoperable.\
**GRUB bootloader and console access required** <br>
<br>
Cloud providers console access list (not exhaustive) : <br>
<br>
AWS: EC2 Instance Connect\
DigitalOcean: Droplet Console\
Linode: Lish\
OVH: KVM\
Vultr: View Console\
GCP and Azure: Serial Console
>