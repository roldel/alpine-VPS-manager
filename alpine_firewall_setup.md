## Alpine Firewall Setup

### Overview

<br>

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

### 1. Install iptables

<br>

Install iptables with the following command:
```sh
apk add iptables
```

<br>

### 2. Set iptables rules

**Run the following script as a whole. Executing it line by line will flush all existing rules, which can terminate your SSH connection.**

This script sets up a basic security policy:

```sh
#!/bin/sh

# Flush existing rules
iptables -F
iptables -X

# Set default policies
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT

# Allow established and related connections
iptables -A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT

# Allow new SSH connections
iptables -A INPUT -p tcp --dport 22 -m conntrack --ctstate NEW -j ACCEPT

#Allow loopback intefrace connections
iptables -A INPUT -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT

```

After running the script, check the rules with:

```sh
iptables -L -vn
```

<br>

### 3. Persist iptables rules

To ensure the rules are enforced after a reboot:

```sh
# Set iptables to start on reboot 
rc-update add iptables 

# Write the firewall rules to disk 
rc-service iptables save

```
<br>


[Back to project README](README.md)