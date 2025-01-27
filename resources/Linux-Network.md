#### Network

```bash
ls /etc/netplan
```

```bash
systemctl is-active NetworkManager
```

```bash
systemctl is-active systemd-networkd
```

**for Legacy ifupdown**

```bash
cat /etc/network/interfaces
```

**See Network Manager per Interface**

```bash
networkctl status
```

#### Network Manager

##### Files

- `/etc/NetworkManager/NetworkManager.conf`: Main configuration file for NetworkManager.
- `/etc/NetworkManager/system-connections`: Configuration files for individual network interfaces.

##### To check network

- **Interface and IP Configuration**:
  - `ip addr`: Display IP addresses of all network interfaces.
  - `ip link`: Show details of all interfaces.
  - `ifconfig` (from `net-tools`): Displays network interfaces and their configurations.
- **Routing Table**:
  - `ip route`: Show routing table.
  - `route -n` (from `net-tools`): Alternative routing table display.
- **DNS**:
  - `cat /etc/resolv.conf`: Show current DNS servers.
  - `nmcli dev show | grep DNS`: Show DNS settings with NetworkManager.
- **Active Connections**:
  - `nmcli connection show`: Show active and saved connections.
  - `iwconfig`: Display wireless network information.
- **Testing Connectivity**:
  - `ping <address>`: Check network connectivity.
  - `traceroute <address>`: Show the route packets take to the destination.

##### ** Commands to Change Network Configuration**

- **Using `nmcli` (NetworkManager Command-Line Tool)**:
  - `nmcli connection up <connection_name>`: Activate a connection.
  - `nmcli connection down <connection_name>`: Deactivate a connection.
  - `nmcli device disconnect <device>`: Disconnect a device.
  - `nmcli connection modify <connection_name> ipv4.addresses <IP/CIDR>`: Set a static IP.
  - `nmcli connection modify <connection_name> ipv4.method manual`: Enable manual IP.
  - `nmcli connection modify <connection_name> ipv4.gateway <gateway>`: Set the gateway.
- **Using `ip` Command**:
  - `ip addr add <IP>/<CIDR> dev <interface>`: Add an IP to an interface.
  - `ip route add <destination> via <gateway>`: Add a route.
  - `ip link set <interface> up`: Bring an interface up.
  - `ip link set <interface> down`: Bring an interface down.

#### Netplan Static IP Configuration on Ubuntu Server

This document provides a detailed guide on configuring a static IP address on an Ubuntu Server using Netplan. Netplan uses YAML syntax for network configuration, simplifying management and customization of network settings. It was introduced in Ubuntu 18.04 and is available in all later versions.

---

### **Software Requirements and Conventions**

| **Category**    | **Requirements or Conventions**                |
| --------------- | ---------------------------------------------- |
| **System**      | Ubuntu Linux (18.04 or newer)                  |
| **Software**    | `netplan.io`                                   |
| **Permissions** | Root or `sudo` access                          |
| **Conventions** | `#` - Commands executed as root                |
|                 | `$` - Commands executed as non-privileged user |

---

### **Netplan Configuration Overview**

- **Default Configuration Locations:**

  - `/etc/netplan/`
  - `/lib/netplan/`
  - `/run/netplan/`

- **Common Configuration Files:**

  - `01-netcfg.yaml`
  - `01-network-manager-all.yaml`
  - `50-cloud-init.yaml`

- **Renderer Options:**
  - `networkd`: Primarily for server environments.
  - `NetworkManager`: Typically used in desktop environments.

**Note:** If no renderer is specified, `networkd` is used by default.

---

### **Dynamic vs. Static IP Configuration**

- **Dynamic IP Address (DHCP):**

  - Assigned automatically by the DHCP server.
  - Easier setup but less control.

- **Static IP Address:**
  - Manually configured on the system.
  - Provides consistent addressing for the server.

---

### **Step-by-Step Static IP Configuration**

1. **Locate the Netplan Configuration File:**

   - Example file: `/etc/netplan/50-cloud-init.yaml`
   - If the file is missing, generate a new configuration:
     ```bash
     $ sudo netplan generate
     ```

2. **Default DHCP Configuration Example:**

   ```yaml
   # This file describes the network interfaces available on your system
   # For more information, see netplan(5).
   network:
     version: 2
     renderer: networkd
     ethernets:
       enp0s3:
         dhcp4: yes
   ```

3. **Change to Static IP Configuration:**
   Replace the DHCP configuration with the following:
   ```yaml
   network:
     version: 2
     renderer: networkd
     ethernets:
       enp0s3:
         dhcp4: false
         dhcp6: false
         addresses:
           - 192.168.1.122/24
         routes:
           - to: default
             via: 192.168.1.1
         nameservers:
           addresses: [8.8.8.8, 8.8.4.4]
   ```

**Note:** Ensure correct indentation in the YAML file to avoid syntax errors.

4. **Apply Configuration:**

   ```bash
    sudo netplan apply
   ```

5. **Debugging Configuration Issues:**

   - Use the debug option to troubleshoot errors:
     ```bash
      sudo netplan --debug apply
     ```

6. **Disable Cloud-Init Network Configuration:**
   - To prevent cloud-init from overwriting manual configurations:
     ```bash
     $ sudo bash -c 'echo "network: {config: disabled}" > /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg'
     ```

---

### **Your Setup: IP Addresses and Gateways**

#### **Host Setup:**

- **Arch Linux Host IP:** `192.168.102.249`
- **Ubuntu Server VM IP:** `192.168.122.209`

#### **Possible IP Address Ranges for Ubuntu Server:**

- If you’re using a NAT-based virtual network:

  - Address Range: `192.168.122.2 - 192.168.122.254`
  - Example IP: `192.168.122.150`
  - Gateway: `192.168.122.1`

- If you’re using a Bridged Adapter:
  - Address Range: Same as your Arch Linux subnet (e.g., `192.168.102.0/24`)
  - Example IP: `192.168.102.150`
  - Gateway: `192.168.102.1`

---

### **Example Configuration for NAT Setup**

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: false
      dhcp6: false
      addresses:
        - 192.168.122.150/24
      routes:
        - to: default
          via: 192.168.122.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
```

### **Example Configuration for Bridged Adapter**

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: false
      dhcp6: false
      addresses:
        - 192.168.102.150/24
      routes:
        - to: default
          via: 192.168.102.1
      nameservers:
        addresses: [1.1.1.1, 8.8.8.8]
```

---

### **Final Notes**

- Verify connectivity after applying changes:
  ```bash
  $ ping 8.8.8.8
  $ ping google.com
  ```
- For troubleshooting, check logs:
  ```bash
  $ sudo journalctl -u systemd-networkd
  ```

##### ** Commands to Change Network Configuration**

- **Using `nmcli` (NetworkManager Command-Line Tool)**:
  - `nmcli connection up <connection_name>`: Activate a connection.
  - `nmcli connection down <connection_name>`: Deactivate a connection.
  - `nmcli device disconnect <device>`: Disconnect a device.
  - `nmcli connection modify <connection_name> ipv4.addresses <IP/CIDR>`: Set a static IP.
  - `nmcli connection modify <connection_name> ipv4.method manual`: Enable manual IP.
  - `nmcli connection modify <connection_name> ipv4.gateway <gateway>`: Set the gateway.
- **Using `ip` Command**:
  - `ip addr add <IP>/<CIDR> dev <interface>`: Add an IP to an interface.
  - `ip route add <destination> via <gateway>`: Add a route.
  - `ip link set <interface> up`: Bring an interface up.
  - `ip link set <interface> down`: Bring an interface down.
### ** Configure the New Network Manager**

#### **A. Configure Netplan**
Netplan uses YAML files in `/etc/netplan/`. Create or modify a configuration file for your network interface.

##### **Dynamic IP (DHCP)**

1. Create or edit `/etc/netplan/01-netcfg.yaml`:
   ```yaml
   network:
     version: 2
     renderer: networkd  # or 'NetworkManager' for desktop setups
     ethernets:
       eth0:  # Replace with your actual interface name
         dhcp4: true
   ```

2. Apply the configuration:
   ```bash
   sudo netplan apply
   ```

##### **Static IP Configuration**
For a static IP configuration, modify the `/etc/netplan/01-netcfg.yaml` as follows:

1. Example static IP configuration:
   ```yaml
   network:
     version: 2
     renderer: networkd
     ethernets:
       eth0:  # Replace with your actual interface name
         addresses:
           - 192.168.1.100/24  # Replace with your desired static IP
         gateway4: 192.168.1.1  # Replace with your gateway IP
         nameservers:
           addresses:
             - 8.8.8.8  # DNS server (you can use other DNS servers like 1.1.1.1)
             - 8.8.4.4
   ```

2. Apply the configuration:
   ```bash
   sudo netplan apply
   ```

#### **B. Configure systemd-networkd**
If you're using **systemd-networkd** (often used on servers), you need to configure `.network` and `.netdev` files.

##### **Dynamic IP (DHCP)**

1. Create or edit the `/etc/systemd/network/20-wired.network` file:
   ```ini
   [Network]
   DHCP=ipv4
```
2. Restart systemd-networkd:
   ```bash
   sudo systemctl restart systemd-networkd
   ```

##### **Static IP Configuration**

1. Edit the same file `/etc/systemd/network/20-wired.network`:
   ```ini
   [Network]
   Address=192.168.1.100/24  # Static IP address
   Gateway=192.168.1.1       # Gateway IP
   DNS=8.8.8.8               # DNS server
```
2. Restart systemd-networkd:
   ```bash
   sudo systemctl restart systemd-networkd
   ```

---

### ** Enable and Start the New Network Manager**
Once you have configured the new network manager, enable and start it to ensure it takes over:

#### **For Netplan (uses systemd-networkd or NetworkManager as renderer)**
- **systemd-networkd:**
  ```bash
  sudo systemctl enable systemd-networkd
  sudo systemctl start systemd-networkd
  ```

- **NetworkManager:**
  ```bash
  sudo systemctl enable NetworkManager
  sudo systemctl start NetworkManager
  ```

#### **For systemd-networkd**
If you're using `systemd-networkd` directly:
```bash
sudo systemctl enable systemd-networkd
sudo systemctl start systemd-networkd
```

---

### ** Verify the New Configuration**
Check the status of your network interface to ensure it's up and running with the correct IP configuration:

```bash
ip addr show eth0  # Replace eth0 with your interface name
```

For dynamic IP configuration, ensure the IP address is assigned correctly via DHCP.

For static IP, verify the correct static IP is assigned.

You can also check the status of network services:

```bash
networkctl status eth0  # Replace eth0 with your interface name
```

---

#### DNS

##### Netplan-Systems
