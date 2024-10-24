# Creating Swap file in Ubuntu

```sh
#check
htop

df -h

free -h

# Allocate 1GB swap file
sudo fallocate -l 1G /swapfile

# Set correct permissions for the swapfile
sudo chmod 600 /swapfile

# Setup swap space
sudo mkswap /swapfile

# Enable swap
sudo swapon /swapfile

# Set swappiness to 10
sudo sysctl vm.swappiness=10

# Set vfs_cache_pressure to 50
sudo sysctl vm.vfs_cache_pressure=50

# Persist swappiness setting in /etc/sysctl.conf
echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.conf

# Persist vfs_cache_pressure setting in /etc/sysctl.conf
echo 'vm.vfs_cache_pressure=50' | sudo tee -a /etc/sysctl.conf

# Persist the swapfile in /etc/fstab for future reboots
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab

```
