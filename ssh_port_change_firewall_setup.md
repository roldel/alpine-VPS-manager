## SSH port change

### Overview

Alpine OS does not come with a pre-installed firewall. To filter connections, follow these steps to install and set up iptables:

1. Install iptables package

2. Set iptables rules

3. Persist iptables rules


<br>

**WARNING :**\
Incorrect configuration while connected to the VPS via SSH can terminate the connection and lock you out. If this happens, you'll need to fix the firewall configuration through your cloud provider's console access.
<br>

<hr>
<br>

**Run the following script as a whole. Executing it line by line will flush all existing rules, which can terminate your SSH connection.**
<br>


```sh
#!/bin/sh

# Define the new port
NEW_PORT=$1



if [ -z "$NEW_PORT" ]; then
  echo "Usage: $0 <new_port>"
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

After running the script, check the rules with:

```sh
iptables -L -vn
```

<br>

[Next step : Docker install](docker_install.md)

<br>


[Back to project README](README.md)