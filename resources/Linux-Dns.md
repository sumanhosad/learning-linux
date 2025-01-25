---

```markdown
# Testing DNS on Linux

DNS (Domain Name System) translates domain names into IP addresses. To troubleshoot or test DNS functionality in Linux, the following tools are commonly used: `ping`, `nslookup`, `dig`, and `host`.

---

## 1. **Using `ping` to Test DNS**

`ping` verifies if a domain name resolves to an IP address and tests connectivity to that address.

### Example:

```bash
ping google.com
```

Output:

```
PING google.com (142.250.74.14): 56 data bytes
64 bytes from 142.250.74.14: icmp_seq=1 ttl=118 time=11.2 ms
```

- If the domain name resolves to an IP address, DNS is working.
- If you get an error like `ping: google.com: Name or service not known`, DNS resolution is failing.

---

## 2. **Using `nslookup`**

`nslookup` queries DNS servers to resolve domain names or check DNS records.

### Query a Domain:

```bash
nslookup google.com
```

Output:

```
Server:         8.8.8.8
Address:        8.8.8.8#53

Non-authoritative answer:
Name:   google.com
Address: 142.250.74.14
```

- `Server`: The DNS server used for the query.
- `Non-authoritative answer`: Response from a cache or intermediate DNS server.

### Query a Specific DNS Server:

```bash
nslookup google.com 1.1.1.1
```

### Reverse DNS Lookup (Find Domain from IP):

```bash
nslookup 142.250.74.14
```

---

## 3. **Using `dig`**

`dig` provides detailed information about DNS queries and is useful for debugging DNS issues.

### Query a Domain:

```bash
dig google.com
```

Output:

```
;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             299     IN      A       142.250.74.14
```

- **QUESTION SECTION**: What is being queried.
- **ANSWER SECTION**: The resolved IP address.

### Query Specific DNS Records:

- Get an `A` (IPv4) record:

  ```bash
  dig google.com A
  ```

- Get an `MX` (Mail Exchange) record:

  ```bash
  dig google.com MX
  ```

- Get an `NS` (Name Server) record:
  ```bash
  dig google.com NS
  ```

### Use a Specific DNS Server:

```bash
dig @1.1.1.1 google.com
```

---

## 4. **Using `host`**

`host` is a simple tool for DNS lookups.

### Query a Domain:

```bash
host google.com
```

Output:

```
google.com has address 142.250.74.14
google.com has IPv6 address 2607:f8b0:4006:80f::200e
```

### Reverse DNS Lookup:

```bash
host 142.250.74.14
```

Output:

```
14.74.250.142.in-addr.arpa domain name pointer ord36s01-in-f14.1e100.net.
```

### Query Specific Records:

- Get `MX` records:

  ```bash
  host -t MX google.com
  ```

- Get `NS` records:
  ```bash
  host -t NS google.com
  ```

---

## 5. **Example Workflow for Testing DNS**

1. **Test Basic DNS Resolution**:

   ```bash
   ping google.com
   ```

   - If successful, DNS is resolving the domain name.

2. **Verify DNS Server in Use**:

   ```bash
   nslookup google.com
   ```

3. **Get Detailed Query Information**:

   ```bash
   dig google.com
   ```

4. **Check for Specific Records**:

   - Example: Fetch mail server details for a domain:
     ```bash
     dig google.com MX
     ```

5. **Perform Reverse DNS Lookup**:
   ```bash
   host 142.250.74.14
   ```

---

## 6. **Common DNS Debugging Issues**

- **DNS Not Resolving**:

  - Check the DNS server in `/etc/resolv.conf`:
    ```bash
    cat /etc/resolv.conf
    ```
  - Update it to use public DNS servers like Google (8.8.8.8) or Cloudflare (1.1.1.1):
    ```bash
    sudo nano /etc/resolv.conf
    ```
    Add:
    ```
    nameserver 8.8.8.8
    nameserver 1.1.1.1
    ```

- **Check Connectivity to the DNS Server**:
  ```bash
  ping 8.8.8.8
  ```

---

By using these tools, you can effectively test and troubleshoot DNS issues in Linux.

```

```
