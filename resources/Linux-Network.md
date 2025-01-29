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
  - `ip link`: Show details of all interfaces. [[#`ip link` output|output]]
  - `ifconfig` (from `net-tools`): Displays network interfaces and their configurations.
- **Routing Table**:
  - `ip route`: Show routing table. [[#Output of ```ip route```|output]]
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
  - `sudo ip addr del <IP_ADDRESS>/<PREFIX> dev <INTERFACE>`


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
### **Configure the New Network Manager**

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

### **Enable and Start the New Network Manager**
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

### **Verify the New Configuration**
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

##### Output of ```ip route```
1. **Default Route:**
   ```bash
   default via 192.168.122.1 dev enp1s0 proto dhcp src 192.168.122.60 metric 100
   ```
   - **default**: This is the default route, meaning it's the fallback route used when no more specific routes match the destination IP address.
   - **via 192.168.122.1**: This is the next-hop gateway IP for the default route, which is the router (or another network device) used to send packets to destinations outside your local subnet.
   - **dev enp1s0**: This specifies the network interface (`enp1s0`) through which the default route will be accessed.
   - **proto dhcp**: The route was configured dynamically using DHCP (Dynamic Host Configuration Protocol).
   - **src 192.168.122.60**: This is the source IP address used when sending packets through this route. It’s the IP address of the local machine on the `enp1s0` interface.
   - **metric 100**: The metric is a cost value that influences the priority of the route. Lower values are preferred. Here, the metric is set to 100, meaning this is not the highest-priority route, but it's the one in use for default routing.

2. **Local Network Route (192.168.122.0/24):**
   ```bash
   192.168.122.0/24 dev enp1s0 proto kernel scope link src 192.168.122.60 metric 100
   ```
   - **192.168.122.0/24**: This is the local network range. The `/24` is the subnet mask (255.255.255.0), which defines the local subnet as IPs between `192.168.122.1` and `192.168.122.254`.
   - **dev enp1s0**: This indicates that the local network `192.168.122.0/24` is accessed via the `enp1s0` interface.
   - **proto kernel**: This route was automatically created by the kernel when the interface `enp1s0` was assigned the IP address `192.168.122.60`.
   - **scope link**: This means the route is limited to the local link, meaning only local machines in the `192.168.122.0/24` subnet can be reached.
   - **src 192.168.122.60**: This is the source IP used for the local network route, the machine's own IP address on the `enp1s0` interface.
   - **metric 100**: The metric is the same as the default route, indicating this route's priority in the routing table.

3. **Gateway IP Route (192.168.122.1):**
   ```bash
   192.168.122.1 dev enp1s0 proto dhcp scope link src 192.168.122.60 metric 100
   ```
   - **192.168.122.1**: This is the IP address of the gateway (the router) for the local network.
   - **dev enp1s0**: The route is accessible through the `enp1s0` network interface.
   - **proto dhcp**: The route to the gateway was set by DHCP.
   - **scope link**: This means the gateway `192.168.122.1` is directly reachable over the local network (same subnet).
   - **src 192.168.122.60**: The source address used when communicating with this gateway is `192.168.122.60`, the local machine's IP.
   - **metric 100**: Again, this indicates the priority of the route is 100, and it’s treated with the same priority as the other routes.

### Summary:
- The default route sends all traffic to the gateway `192.168.122.1` via the `enp1s0` interface.
- The machine is directly connected to the local network `192.168.122.0/24` using the same interface.
- The gateway `192.168.122.1` is directly reachable on the same subnet, and all of this is managed through DHCP.


##### `ip link` output:

1. **Loopback Interface (`lo`)**:
   ```bash
   1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
       link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
   ```
   - **lo**: This is the loopback interface, which is used for internal communication within the local system.
   - **<LOOPBACK,UP,LOWER_UP>**: These are the interface flags:
     - **LOOPBACK**: Indicates that this is a loopback interface.
     - **UP**: The interface is administratively up (enabled).
     - **LOWER_UP**: The physical link is up (i.e., it's working).
   - **mtu 65536**: The Maximum Transmission Unit (MTU) size for this interface is set to 65536 bytes, which is typical for loopback interfaces.
   - **qdisc noqueue**: No queuing discipline is applied to this interface. The loopback interface typically doesn't need it.
   - **state UNKNOWN**: The state of the interface is "UNKNOWN," which is common for loopback interfaces since they don't have a physical status.
   - **mode DEFAULT**: The mode is set to "DEFAULT," meaning the interface is in its default operational mode.
   - **group default**: The interface belongs to the default group.
   - **qlen 1000**: The length of the transmission queue is set to 1000 packets.
   - **link/loopback 00:00:00:00:00:00**: The link type is "loopback," and it has a placeholder MAC address (`00:00:00:00:00:00`), as loopback interfaces don’t need a real MAC address.

2. **Ethernet Interface (`enp1s0`)**:
   ```bash
   2: enp1s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
       link/ether 52:54:00:77:86:c4 brd ff:ff:ff:ff:ff:ff
   ```
   - **enp1s0**: This is an Ethernet interface. The name `enp1s0` indicates it is the first Ethernet interface (`en`) on PCI bus 1 (`p1`), slot 0 (`s0`).
   - **<BROADCAST,MULTICAST,UP,LOWER_UP>**: These are the interface flags:
     - **BROADCAST**: This interface supports broadcasting, which means it can send packets to all devices in the network.
     - **MULTICAST**: The interface supports multicast, which allows sending packets to multiple devices at once.
     - **UP**: The interface is administratively up (enabled).
     - **LOWER_UP**: The physical link is up (i.e., the interface is connected to a network).
   - **mtu 1500**: The Maximum Transmission Unit (MTU) is set to 1500 bytes, which is typical for Ethernet interfaces.
   - **qdisc fq_codel**: The queuing discipline is set to **fq_codel**, which is used for managing traffic flow and reducing latency.
   - **state UP**: The interface is in the "UP" state, meaning it is operational and can send/receive traffic.
   - **mode DEFAULT**: The mode is set to "DEFAULT," indicating that the interface is using the standard configuration.
   - **group default**: The interface belongs to the default group.
   - **qlen 1000**: The length of the transmission queue is set to 1000 packets.
   - **link/ether 52:54:00:77:86:c4**: This is the hardware (MAC) address of the interface, which is a unique identifier for the network card.
   - **brd ff:ff:ff:ff:ff:ff**: This is the broadcast address for the interface. It's used to send packets to all devices in the local network.

### Summary:
- The **lo** interface is used for internal communication within the local machine.
- The **enp1s0** interface is an Ethernet interface that connects the system to an external network. It is administratively up, has a typical MTU of 1500 bytes, and supports broadcast and multicast traffic.
