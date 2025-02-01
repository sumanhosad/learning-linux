## Network

#### [[NetPlan#Netplan|NetPlan]]
```bash
ls /etc/netplan
```

#### [[Network-Manager#Network-Manager|Network-Manager]]
```bash
systemctl is-active NetworkManager
```

#### [[Systemd-Networkd#**systemd-networkd**|Systemd-Networkd]]
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

#### [[DHCP#DHCP|DHCP]]

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

- **Using `ip` Command**:
  - `ip addr add <IP>/<CIDR> dev <interface>`: Add an IP to an interface.
  - `ip route add <destination> via <gateway>`: Add a route.
  - `ip link set <interface> up`: Bring an interface up.
  - `ip link set <interface> down`: Bring an interface down.

