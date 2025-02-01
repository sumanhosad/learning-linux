### Netplan

#### Netplan 

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

