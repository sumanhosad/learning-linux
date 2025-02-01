#### **systemd-networkd**
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
