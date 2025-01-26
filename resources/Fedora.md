# Fedora

#### Network

---

##### **1. File Names and Paths of Network Configuration Files**

- **NetworkManager Configurations**:
  - `/etc/NetworkManager/NetworkManager.conf`: Main configuration file for NetworkManager.
  - `/etc/NetworkManager/system-connections`: Configuration files for individual network interfaces.
- **systemd-networkd Configurations**:
  - `/etc/systemd/network/*.network`: Network files defining the interface and configuration.
  - `/etc/systemd/network/*.netdev`: Network device configurations.
  - `/etc/systemd/network/*.link`: Link-specific configurations.
- **DNS Configurations**:
  - `/etc/resolv.conf`: File used for system DNS settings.
- **Firewall**:
  - `/etc/firewalld`: Firewall settings directory.

---

##### **2. Commands to Show Current Network Configuration**

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

---

##### **3. Commands to Change Network Configuration**

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

---

##### **4. Configuring DHCP and Static IP**

- **DHCP Configuration**:
  - With NetworkManager:
    - `nmcli connection modify <connection_name> ipv4.method auto`: Enable DHCP.
    - Restart the connection: `nmcli connection up <connection_name>`.
  - Manually:
    - Ensure `/etc/sysconfig/network-scripts/ifcfg-<interface>` contains:
      ```
      BOOTPROTO=dhcp
      ONBOOT=yes
      ```
    - Restart the network: `systemctl restart NetworkManager`.
- **Static IP Configuration**:
  - With NetworkManager:
    - `nmcli connection modify <connection_name> ipv4.addresses <IP/CIDR> ipv4.gateway <gateway> ipv4.method manual`.
    - Set DNS: `nmcli connection modify <connection_name> ipv4.dns <DNS_IP>`.
    - Restart the connection: `nmcli connection up <connection_name>`.
  - Manually:
    - Edit `/etc/sysconfig/network-scripts/ifcfg-<interface>`:
      ```
      BOOTPROTO=none
      IPADDR=<IP>
      NETMASK=<Netmask>
      GATEWAY=<Gateway>
      ONBOOT=yes
      ```
    - Restart the network: `systemctl restart NetworkManager`.

---

##### **5. Configuring and Accessing DNS**

- **Check Current DNS Settings**:
  - `cat /etc/resolv.conf`: Lists active DNS servers.
  - `nmcli dev show | grep DNS`: DNS used by NetworkManager.
- **Configure DNS with NetworkManager**:
  - `nmcli connection modify <connection_name> ipv4.dns <DNS_IP>`.
  - Restart the connection: `nmcli connection up <connection_name>`.
- **Manually Edit `/etc/resolv.conf`**:
  - Add:
    ```
    nameserver <DNS_IP>
    ```
  - Note: Changes may be overwritten by network managers unless made persistent.

---

##### **6. Distinguishing Between `systemd-networkd` and NetworkManager**

- **NetworkManager**:
  - Default on Fedora.
  - GUI, CLI (`nmcli`), and TUI (`nmtui`) tools available.
  - Ideal for laptops and desktops with frequently changing networks.
- **systemd-networkd**:
  - Lightweight and systemd-integrated.
  - Preferred for servers or minimal installations.
  - Files: `/etc/systemd/network/`.
- **Other Managers**:
  - `wicd`: Lightweight and GUI-based.
  - `connman`: Lightweight network connection manager.

---

##### **7. Comprehensive Guide**

1. **Identify Your Network Manager**:
   - Check active service: `systemctl is-active NetworkManager` or `systemctl is-active systemd-networkd`.
2. **View Current Configuration**:
   - Use `ip`, `nmcli`, or `systemctl` commands to analyze settings.
3. **Change Network Settings**:
   - Use `nmcli` for simplicity or manual editing for advanced control.
4. **Set Up DHCP and Static IP**:
   - Use either NetworkManager or manual file edits for configuration.
5. **DNS Configuration**:
   - Ensure proper DNS settings in `resolv.conf` or via `nmcli`.
6. **Experiment and Practice**:
   - Test different configurations and verify with tools like `ping` and `traceroute`.
