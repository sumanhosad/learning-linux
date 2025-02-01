#### Kernel-Module

Here’s an overview of how to load, unload, and manage kernel modules in Linux, along with their usage.

---

# Managing Kernel Modules in Linux

Kernel modules are pieces of code that can be dynamically loaded into the kernel to extend its functionality. Examples include device drivers, file systems, and network protocols.

---

## Viewing Loaded Modules

To see a list of all currently loaded modules, use the `lsmod` command:

```bash
lsmod
```

This will display:

- Module name
- Size of the module
- Number of processes or devices using it

To get detailed information about a specific module:

```bash
modinfo <module_name>
```

---

## Loading a Kernel Module

### 1. **Load with `modprobe`** (Preferred Method)

`modprobe` is the standard tool for managing modules. It automatically resolves dependencies.

```bash
sudo modprobe <module_name>
```

### 2. **Load with `insmod`**

Use `insmod` if you need to load a module manually. Unlike `modprobe`, it does not handle dependencies.

```bash
sudo insmod /path/to/module.ko
```

Here, `.ko` is the compiled kernel module file.

---

## Unloading a Kernel Module

### 1. **Unload with `modprobe`** (Preferred Method)

```bash
sudo modprobe -r <module_name>
```

### 2. **Unload with `rmmod`**

```bash
sudo rmmod <module_name>
```

> Note: If a module is in use, it cannot be unloaded unless you force it with `-f` (not recommended unless absolutely necessary).

---

## Listing Module Dependencies

To list all dependencies of a module, use:

```bash
modinfo <module_name>
```

---

## Configuring Modules to Load at Boot

1. Add the module name to the `/etc/modules` file:

   ```bash
   echo "<module_name>" | sudo tee -a /etc/modules
   ```

2. Alternatively, create a custom `.conf` file in `/etc/modprobe.d/`:

   ```bash
   echo "options <module_name> <parameters>" | sudo tee /etc/modprobe.d/<custom_name>.conf
   ```

3. Update the initramfs to apply changes:
   ```bash
   sudo update-initramfs -u
   ```

---

## Blacklisting a Module

To prevent a module from loading, blacklist it by adding its name to a `.conf` file in `/etc/modprobe.d/`:

```bash
echo "blacklist <module_name>" | sudo tee /etc/modprobe.d/blacklist.conf
```

Then update the initramfs:

```bash
sudo update-initramfs -u
```

---

## Debugging Module Issues

1. Check the `dmesg` logs for errors related to modules:

   ```bash
   dmesg | grep <module_name>
   ```

2. Ensure the module matches the current kernel version:

   ```bash
   uname -r
   modinfo <module_name>
   ```

3. Rebuild modules after kernel updates:
   ```bash
   sudo dkms autoinstall
   ```

---

## Common Module Commands Summary

| Command                | Description                            |
| ---------------------- | -------------------------------------- |
| `lsmod`                | List all loaded modules                |
| `modinfo <module>`     | Show details about a module            |
| `modprobe <module>`    | Load a module (handles dependencies)   |
| `modprobe -r <module>` | Unload a module (handles dependencies) |
| `insmod <module.ko>`   | Manually load a module                 |
| `rmmod <module>`       | Manually unload a module               |

---

By effectively managing kernel modules, you can extend and control the functionality of your Linux system.
