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

Login as root :
```sh
su root
```

Create a new file ( e.g. port_change_firewall.sh ) and save into it the following script :



```sh
#!/bin/sh

# Define the new port
NEW_PORT=$1


# Check $NEW_PORT variable set and not empty
if [ -z "$NEW_PORT" ]; then
  echo "Usage: $0 <new_port>"
  exit 1
# Check $NEW_PORT is a number
elif ! echo "$NEW_PORT" | grep -qE '^[0-9]+$'; then
  echo "Error: The provided port is not a valid number."
  exit 1
# Check $NEW_PORT within expected availble port range  
elif [ "$NEW_PORT" -lt 1024 ] || [ "$NEW_PORT" -gt 65535 ]; then
  echo "Error: The port number must be between 1024 and 65535."
  exit 1
fi

# Check if OpenSSH is installed
if ! command -v sshd > /dev/null 2>&1; then
  echo "OpenSSH is not installed. Installing..."
  apk add openssh
fi

# Define configuration paths
SSHD_CONFIG="/etc/ssh/sshd_config"
SSHD_CONFIG_DIR="/etc/ssh/sshd_config.d"
BACKUP_SUFFIX=".bak"

# Backup the original sshd_config
if [ ! -f "${SSHD_CONFIG}${BACKUP_SUFFIX}" ]; then
  echo "Backing up the original sshd_config to sshd_config.bak"
  cp "$SSHD_CONFIG" "${SSHD_CONFIG}${BACKUP_SUFFIX}"
fi

# Function to update the port in a given file
update_port() {
  FILE=$1
  sed -i "/^#Port 22/c\Port $NEW_PORT" "$FILE"
  sed -i "/^Port [0-9]*$/c\Port $NEW_PORT" "$FILE"
}

# Update the main sshd_config file
update_port "$SSHD_CONFIG"

# Update any additional configuration files in sshd_config.d
if [ -d "$SSHD_CONFIG_DIR" ]; then
  for FILE in "$SSHD_CONFIG_DIR"/*.conf; do
    if [ -f "$FILE" ]; then
      echo "Updating port in $FILE"
      update_port "$FILE"
    fi
  done
fi


# Check if iptables is installed
if ! command -v iptables > /dev/null 2>&1; then
  echo "iptables is not installed. Installing..."
  apk add iptables
fi

# Flush existing rules
iptables -F
iptables -X

# Set default policies
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT

# Allow established and related connections
iptables -A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT

#Allow loopback intefrace connections
iptables -A INPUT -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT


# Add iptables rule to accept incoming connections on the new port
echo "Adding iptables rule to accept incoming connections on port $NEW_PORT"
iptables -I INPUT -p tcp --dport "$NEW_PORT" -j ACCEPT

# Save the iptables rules to persist after reboot
rc-update add iptables 
rc-service iptables save

# Restart the SSH service
echo "Restarting the SSH service"
rc-service sshd restart

echo "SSH port changed to $NEW_PORT and iptables rule added to accept incoming connections"
```

<br>

Execute the script and pass it the new desired port number as an argument :

```sh
sh port_change_firewall.sh 2222
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

[Next step : Docker install](docker_install.md)

<br>


[Back to project README](README.md)