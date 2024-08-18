## Alpine Default SSH Port Change and Firewall Configuration

### Overview

This shell script is designed to change the SSH default port on an Alpine Linux server and configure the firewall rules.\
It enforces a basic security policy and allows incoming ssh connections on the new port.\
Given that Alpine Linux does not come with a pre-installed firewall, the script includes steps to install iptables and ensures that the firewall rules are persistent across reboots.

<br>

**WARNING :**\
This script is intended for the initial setup on a freshly installed Alpine Linux OS.\
Before running the script, be aware of the following:

Disruption of Service:
Flushing existing iptables rules will disrupt any current network connections and services. Since this script is intended for a fresh installation, this should not be an issue.

Alpine Linux Specific:
This script is specifically designed for Alpine Linux. Running it on other distributions may cause errors or unexpected behavior due to differences in package managers and system configurations.

Post-Configuration Access:
After running the script, you will need to reconnect to your server using the new SSH port. Ensure that your client settings are updated accordingly to avoid access issues.

**Run the following script as a whole. Executing it line by line will flush all existing rules, which can terminate your SSH connection.**

<br>

<hr>

<br>


Download the script from our repository :
<br>



```sh
wget https://raw.githubusercontent.com/roldel/alpine-vps-manager/main/scripts/port_change_firewall_setup.sh
```

<br>

Execute the script, passing it the new desired port number as an argument :

```sh
sh port_change_firewall_setup.sh 2222
```

After running the script, check the rules with:

```sh
iptables -L -vn
```

For further SSH connections, specify the updated SSH port number:

```sh
ssh <username>@<ipaddress> -p <new_port_number>
```

<br>

### Further considerations :

Security possible additional layers :

  - Install fail2ban
  - limit connection over ipv6

<br> 

[Next step : Docker install](docker_install.md)

<br>


[Back to project README](README.md)