# Creating a Swap File on Ubuntu

## Step 1: Check System Resources

Check system resources to see if you need additional swap memory:

```sh
htop
df -h
free -h
```

---

## Step 2: Allocate a Swap File

Allocate a 1GB swap file (or adjust size as needed):

```sh
sudo fallocate -l 1G /swapfile
```

---

## Step 3: Set Permissions

Set correct permissions to ensure only root can access the swap file:

```sh
sudo chmod 600 /swapfile
```

---

## Step 4: Set Up the Swap Space

Format the file as swap space:

```sh
sudo mkswap /swapfile
```

---

## Step 5: Enable Swap

Enable the swap file:

```sh
sudo swapon /swapfile
```

---

## Step 6: Adjust Swappiness and Cache Pressure

Set `swappiness` to 10 (recommended for SSDs or servers):

```sh
sudo sysctl vm.swappiness=10
```

Set `vfs_cache_pressure` to 50 to retain more cached filesystem metadata:

```sh
sudo sysctl vm.vfs_cache_pressure=50
```

---

## Step 7: Persist Settings in `/etc/sysctl.conf`

Make swappiness and cache pressure settings persist after reboot:

```sh
echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.conf
echo 'vm.vfs_cache_pressure=50' | sudo tee -a /etc/sysctl.conf
```

---

## Step 8: Persist the Swap File in `/etc/fstab`

Add the swap file to `/etc/fstab` to enable it on reboot:

```sh
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

---

This completes the setup for creating a swap file on Ubuntu.
