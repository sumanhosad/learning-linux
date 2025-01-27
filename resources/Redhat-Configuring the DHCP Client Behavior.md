# Configuring the DHCP Client Behavior

## Overview
A **Dynamic Host Configuration Protocol (DHCP)** client requests a dynamic IP address and corresponding configuration information from a DHCP server each time it connects to the network. By default, **NetworkManager** calls the DHCP client, `dhclient`.

---

## Requesting an IP Address
When a DHCP connection is initiated, the DHCP client requests an IP address from the DHCP server. The default time for this request to complete is **60 seconds**. This can be configured using the `ipv4.dhcp-timeout` property.

### Configuring the DHCP Timeout Using `nmcli`
To change the timeout:
```bash
nmcli connection modify enp1s0 ipv4.dhcp-timeout 10
```

### Behavior When the Address Cannot Be Obtained
- **IPv4 Configuration Fails** if the address is not obtained within the timeout interval.
- **Connection State** depends on the `ipv4.may-fail` property:
  - If `ipv4.may-fail=yes` (default):
    - If IPv6 is enabled and successfully configured, the connection is activated, but IPv4 cannot be retried.
    - If IPv6 is disabled or not configured, the connection fails.
  - If `ipv4.may-fail=no`:
    - The connection is deactivated.
    - If the `autoconnect` property is enabled, **NetworkManager** retries activation based on the `autoconnect-retries` property (default: 4).
    - If the connection fails repeatedly, auto-activation restarts after 5 minutes.

---

## Requesting a Lease Renewal
When a DHCP address is acquired, the lease must be periodically renewed. If renewal fails, the DHCP client behaves as follows:

1. **Retries**:
   - The client restarts **three times** every **2 minutes** to renew the lease.
   - The `ipv4.dhcp-timeout` property (default: 60 seconds) determines the timeout for each attempt.
   - If the server responds, the lease is successfully renewed, and the retry process stops.

2. **If Renewal Fails After Three Attempts**:
   - If `ipv4.may-fail=yes` (default):
     - If IPv6 is successfully configured, the connection remains active.
     - The DHCP client is restarted every 2 minutes to retry the renewal process.
   - If `ipv4.may-fail=no`:
     - The connection is deactivated.
     - If `autoconnect` is enabled, the connection is restarted from scratch.

---

## Making DHCPv4 Persistent
To ensure the DHCP client persists during startup and lease renewal processes, configure the `ipv4.dhcp-timeout` property.

### Setting the Timeout to Infinite
Set the timeout to the maximum value for a 32-bit integer (`2147483647`) or `infinity`:
```bash
nmcli connection modify enp1s0 ipv4.dhcp-timeout infinity
```
- **Effect**: The DHCP client will continuously attempt to acquire or renew a lease until successful.

### Ensuring Persistence Only During Lease Renewal
You can configure a static IP to preserve the IP state during the lease renewal process:
```bash
nmcli connection modify enp1s0 ipv4.address 192.168.122.88/24
```
- **Effect**:
  - When the lease expires, the static IP preserves the network configuration (e.g., maintaining an IP address but potentially losing internet connectivity).
  - The DHCP client retries every 2 minutes to obtain a new lease.

---

## Summary
- **Key Configurations**:
  - `ipv4.dhcp-timeout`: Determines how long the DHCP client waits for an address (default: 60 seconds).
  - `ipv4.may-fail`: Defines whether the connection can remain active if IPv4 configuration fails (default: `yes`).
  - `autoconnect`: Automatically retries activation if the connection fails.

- **Commands**:
  - Set DHCP timeout:  
    ```bash
    nmcli connection modify <connection-name> ipv4.dhcp-timeout <value>
    ```
  - Make DHCP persistent:  
    ```bash
    nmcli connection modify <connection-name> ipv4.dhcp-timeout infinity
    ```
  - Configure a static IP:  
    ```bash
    nmcli connection modify <connection-name> ipv4.address <ip-address>/<subnet-mask>
    ```

This configuration ensures robust DHCP behavior during both initial IP acquisition and lease renewal.