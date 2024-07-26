# ALPINE VPS MANAGER

Alpine Linux is a security-oriented, lightweight Linux distribution. Unfortunately, it does not always come as an option, amongst the default OS offered by cloud providers.
<br>
By following this guide, you will set up a secure and efficient Alpine Linux environment on your VPS.

- [Alpine OS installation on VPS ](alpine_os_vps_installation.md)

- [Alpine default SSH port change and firewall configuration](ssh_port_change_firewall_setup.md)

- [Alpine Docker install](docker_install.md)

<br>

<hr>

**WARNING :**\
This guide assumes a basic level of familiarity with Linux command-line operations.\
**The Alpine installation will completely overwrite the current OS and all existing files.**
Proceed with caution and ensure that you have backups of any important data before making significant changes to your VPS configuration.\
Improper handling of some steps, particularly those involving bootloader and partition changes, can render your VPS inoperable.\
**GRUB bootloader and console access required** <br>

<hr>

Cloud providers console access list (not exhaustive) :<br>
<br>
AWS: EC2 Instance Connect\
DigitalOcean: Droplet Console\
Linode: Lish\
OVH: KVM\
Vultr: View Console\
GCP and Azure: Serial Console