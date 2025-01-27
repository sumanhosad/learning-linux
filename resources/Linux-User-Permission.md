### 1. **Creating a New User**

To create a new user, use the `useradd` command:

```bash
sudo useradd -m username
```
- The `-m` option creates the user's home directory.
- Replace `username` with your desired username.

Then, set a password for the new user:

```bash
sudo passwd username
```
- This will prompt you to set a password for the user.

### 2. **Adding the User to the `sudoers` Group**

- On **Debian-based** systems (like Ubuntu), users who need sudo access are typically added to the `sudo` group.
- On **Red Hat-based** systems (like CentOS), users are added to the `wheel` group for sudo access.

#### On Debian/Ubuntu:
```bash
sudo usermod -aG sudo username
```
- The `-aG` flag adds the user to the specified group (`sudo` in this case) without removing them from other groups.

#### On Red Hat/CentOS:
```bash
sudo usermod -aG wheel username
```

#### If usermod is not available use gpasswd
```bash
sudo gpasswd -a username sudo
```
### 3. **Editing the `sudoers` File for Fine-Grained Permissions**

If you want to customize permissions, such as limiting what the user can do with `sudo`, you’ll need to edit the `/etc/sudoers` file. 

**Important:** Always use `visudo` to edit this file. It performs syntax checking to prevent breaking sudo access.

```bash
sudo visudo
```

Add this line at the end of the file, replacing username with the actual username:

```bash
username ALL=(ALL:ALL) ALL
```

You can add specific permissions. For example, to allow a user to run only certain commands with `sudo`, you can add the following:

```bash
username ALL=(ALL) /usr/bin/command1, /usr/bin/command2
```

This gives the user permission to run `command1` and `command2` with `sudo`, but no other commands.

### 4. **Limiting Permissions to Certain Commands Using `sudo`**

You can configure `sudo` to allow a user to execute specific commands with elevated privileges. For example, if you want the user to run `ls` and `cat` as root:

```bash
username ALL=(ALL) NOPASSWD: /bin/ls, /bin/cat
```

This allows the user to run `ls` and `cat` with `sudo` without needing a password.

### 5. **Managing File Permissions**

File permissions in Linux are managed using `chmod`, `chown`, and `chgrp` commands.

#### a. **Understanding File Permissions**

You can check the file permissions using the `ls -l` command:

```bash
ls -l filename
```

The output will look something like this:
```
-rwxr-xr-- 1 user group 4096 Jan 27 12:34 filename
```
- The first part (`-rwxr-xr--`) represents the permissions.
  - `r` = read, `w` = write, `x` = execute.
  - The first three characters are for the owner.
  - The next three are for the group.
  - The final three are for others.

#### b. **Changing Permissions with `chmod`**

- **Add permissions:**

```bash
chmod +x filename
```
This adds execute permission to the file.

- **Remove permissions:**

```bash
chmod -x filename
```
This removes execute permission.

- **Set specific permissions (e.g., rwxr-xr--):**

```bash
chmod 755 filename
```
Where `755` corresponds to:
- `7` (rwx) for the owner.
- `5` (r-x) for the group.
- `5` (r-x) for others.

#### c. **Changing Ownership with `chown`**

You can change the file's owner and group using `chown`:

```bash
sudo chown username:groupname filename
```
- Replace `username` with the desired owner.
- Replace `groupname` with the desired group.
- Replace `filename` with the file or directory.

#### d. **Changing Group Ownership with `chgrp`**

You can change only the group of a file with `chgrp`:

```bash
sudo chgrp groupname filename
```

### 6. **Using `sudo` with Limited Commands (sudoers Example)**

If you want to restrict the user to running only certain commands with `sudo`, you can set up specific rules. For example:

```bash
username ALL=(ALL) NOPASSWD: /usr/bin/top, /usr/bin/ps
```

This gives `username` permission to run `top` and `ps` commands as root without entering a password.

### 7. **Testing the Configuration**

To verify the user can use `sudo`, switch to their account and try running a command with `sudo`:

```bash
su - username
sudo ls /root
```

If configured correctly, the user should have access to the command you specified.

### 8. **Other Useful Commands for User and Group Management**

- **List all users**:  
  ```bash
  cat /etc/passwd
  ```

- **List all groups**:  
  ```bash
  cat /etc/group
  ```

- **List user groups**:  
  ```bash
  groups username
  ```

- **Remove user**:  
  ```bash
  sudo userdel username
  ```

- **Remove user and home directory**:  
  ```bash
  sudo userdel -r username
  ```

- **Remove user from a group**:  
  ```bash
  sudo gpasswd -d username groupname
  ```

### 9. **Checking and Verifying Permissions**

- To check if a user can run a command with `sudo`:

```bash
sudo -l
```

This lists the commands a user can run with `sudo`.

---
