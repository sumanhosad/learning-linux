### Network-Manager
##### Files

- `/etc/NetworkManager/NetworkManager.conf`: Main configuration file for NetworkManager.
- `/etc/NetworkManager/system-connections`: Configuration files for individual network interfaces.

- ##### **Commands to Change Network Configuration**

	- **Using `nmcli` (NetworkManager Command-Line Tool)**:
	  - `nmcli connection up <connection_name>`: Activate a connection.
	  - `nmcli connection down <connection_name>`: Deactivate a connection.
	  - `nmcli device disconnect <device>`: Disconnect a device.
	  - `nmcli connection modify <connection_name> ipv4.addresses <IP/CIDR>`: Set a static IP.
	  - `nmcli connection modify <connection_name> ipv4.method manual`: Enable manual IP.
	  - `nmcli connection modify <connection_name> ipv4.gateway <gateway>`: Set the gateway.

- **Start and Enable NetworkManager**
  ```bash
  sudo systemctl enable NetworkManager
  sudo systemctl start NetworkManager
  ```
