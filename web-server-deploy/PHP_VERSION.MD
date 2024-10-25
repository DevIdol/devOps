# Changing PHP Version on Ubuntu Server

## Step 1: Check Current PHP Version

Before making any changes, check the currently installed PHP version.

```sh
php -v
```

## Step 2: Remove Existing PHP Version

To change the PHP version, you may first need to remove the current version. Replace `php8.3` with your current version if necessary.

```sh
sudo apt remove php8.3 php8.3-* -y
```

## Step 3: Add the Required PHP Repository

To install a different version of PHP, you may need to add a repository that contains the desired PHP version.

```sh
sudo add-apt-repository ppa:ondrej/php -y
sudo apt update
```

## Step 4: Install Desired PHP Version

To **upgrade** or **downgrade**, specify the desired version. Replace `php8.3` with your target version (e.g., `php8.0`, `php8.1`, `php8.2`, etc.).

### Example for Upgrading/Downgrading to PHP 8.1

```sh
sudo apt install php8.1 php8.1-fpm php8.1-cli php8.1-common php8.1-mbstring php8.1-gd php8.1-intl php8.1-xml php8.1-mysql php8.1-zip php8.1-bcmath php8.1-soap php8.1-redis php8.1-curl php8.1-imagick php8.1-xmlrpc php8.1-xsl -y
```

### Example for Upgrading/Downgrading to PHP 8.2

```sh
sudo apt install php8.2 php8.2-fpm php8.2-cli php8.2-common php8.2-mbstring php8.2-gd php8.2-intl php8.2-xml php8.2-mysql php8.2-zip php8.2-bcmath php8.2-soap php8.2-redis php8.2-curl php8.2-imagick php8.2-xmlrpc php8.2-xsl -y
```

## Step 5: Verify Installation

Once the installation is complete, verify that the desired version is installed.

```sh
php -v
```

## Step 6: Configure PHP (if needed)

If you have multiple versions of PHP installed, you may need to set the default version using the `update-alternatives` command.

```sh
sudo update-alternatives --set php /usr/bin/php8.1  # Replace with your desired version
```

## Step 7: Restart Web Server

After changing the PHP version, restart your web server to apply the changes.

### For Nginx

```sh
sudo systemctl restart nginx
```

### For Apache

```sh
sudo systemctl restart apache2
```

## Conclusion

You have successfully changed the PHP version on your Ubuntu server. Make sure to test your applications to ensure compatibility with the new PHP version.

---
