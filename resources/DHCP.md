### **DHCP Overview**  
DHCP dynamically assigns IP addresses and network settings to devices, simplifying network configuration. The DHCP client (`dhclient` by default) handles this process.

---

### **Configuring DHCP Client Behavior**

1. **IP Address Request**  
   - Default timeout for a server response is **60 seconds**.  
   - Adjust using `nmcli`:
     ```bash
     nmcli connection modify <interface> ipv4.dhcp-timeout <seconds>
     ```
   - Behavior based on `ipv4.may-fail`:
     - `yes` (default): Connection continues with IPv6 if IPv4 fails.  
     - `no`: Connection deactivates if IPv4 fails.

2. **Lease Renewal**  
   - Retries **3 times** at 2-minute intervals if the lease expires.  
   - If retries fail:  
     - `ipv4.may-fail=yes`: Connection remains active with IPv6.  
     - `ipv4.may-fail=no`: Connection deactivates, retries follow `autoconnect` settings.

3. **Persistent DHCP**  
   - Keep retrying indefinitely:  
     ```bash
     nmcli connection modify <interface> ipv4.dhcp-timeout infinity
     ```
   - Set a static fallback IP to maintain connectivity:  
     ```bash
     nmcli connection modify <interface> ipv4.address <IP>/<mask>
     ```

---

### **Setting DHCP for Different Network Configurations**

1. **Using `nmcli` (Recommended):**  
   Enable DHCP:
   ```bash
   nmcli connection modify <interface> ipv4.method auto
   nmcli connection up <interface>
   ```

2. **Editing Configuration Files:**  
   - File: `/etc/sysconfig/network-scripts/ifcfg-<interface>`  
     Set these values:
     ```
     BOOTPROTO=dhcp
     ONBOOT=yes
     ```

3. **Using `nmtui` (Text-based UI):**  
   - Run:
     ```bash
     nmtui
     ```
   - Select your connection, set **IPv4 Configuration** to "Automatic", and save changes.

4. **Manual `dhclient` Command:**  
   Start the DHCP client:
   ```bash
   dhclient <interface>
   ```

These methods allow setting DHCP on **NetworkManager**, legacy config files, or directly via commands.