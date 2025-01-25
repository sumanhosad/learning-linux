---

```markdown
# Configuring and Verifying Network Connections in Linux

In Linux, network configuration and verification can be achieved using tools like `ip addr`, `ip route`, and `ping`. These commands provide control over IP addresses, routes, and connectivity testing.

---

## **1. Viewing and Configuring IP Addresses**

### Viewing IP Addresses

Use the `ip addr` command to view details about network interfaces and their IP addresses:

```bash
ip addr
```

Output example:

```
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    inet 192.168.1.10/24 brd 192.168.1.255 scope global dynamic enp0s3
       valid_lft 86399sec preferred_lft 86399sec
```

- `inet 192.168.1.10/24`: The IPv4 address and subnet mask of the interface.
- `state UP`: Indicates the interface is active.

### Configuring an IP Address

To assign a static IP address to a network interface:

```bash
sudo ip addr add 192.168.1.100/24 dev enp0s3
```

To remove an IP address:

```bash
sudo ip addr del 192.168.1.100/24 dev enp0s3
```

---

## **2. Viewing and Configuring Routes**

### Viewing Routes

To view the current routing table, use:

```bash
ip route
```

Output example:

```
default via 192.168.1.1 dev enp0s3 proto dhcp src 192.168.1.10 metric 100
192.168.1.0/24 dev enp0s3 proto kernel scope link src 192.168.1.10
```

- `default via 192.168.1.1`: Default gateway through which traffic to unknown networks is routed.
- `192.168.1.0/24`: Local subnet routing entry.

### Configuring Routes

To add a route to a specific network:

```bash
sudo ip route add 192.168.2.0/24 via 192.168.1.1 dev enp0s3
```

To delete a route:

```bash
sudo ip route del 192.168.2.0/24
```

To change the default gateway:

```bash
sudo ip route replace default via 192.168.1.254 dev enp0s3
```

---

## **3. Testing Network Connectivity**

### Using `ping` to Verify Connectivity

`ping` is used to test connectivity between two hosts by sending ICMP echo requests.

- To test connectivity to a remote host:

  ```bash
  ping google.com
  ```

  Output example:

  ```
  PING google.com (142.250.74.14): 56 data bytes
  64 bytes from 142.250.74.14: icmp_seq=1 ttl=118 time=12.3 ms
  ```

- To limit the number of packets sent:

  ```bash
  ping -c 4 google.com
  ```

- To ping a specific IP address:
  ```bash
  ping 192.168.1.1
  ```

---

## **Example: Configuring and Verifying a Network Connection**

1. **View Current Configuration**:

   ```bash
   ip addr
   ip route
   ```

2. **Assign a Static IP Address**:

   ```bash
   sudo ip addr add 192.168.1.100/24 dev enp0s3
   ```

3. **Set Default Gateway**:

   ```bash
   sudo ip route add default via 192.168.1.1
   ```

4. **Verify Connectivity**:

   - Test the local gateway:
     ```bash
     ping 192.168.1.1
     ```
   - Test external connectivity:
     ```bash
     ping google.com
     ```

5. **Check Updated Routes**:
   ```bash
   ip route
   ```

---

## **Notes**

- Configuration made with `ip` commands is temporary and will reset after a reboot. To make it persistent, modify the network configuration files (e.g., `/etc/network/interfaces` or `/etc/netplan/` depending on your distribution).
- Use `man <command>` (e.g., `man ip`) for detailed information about the commands.

---
