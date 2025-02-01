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


