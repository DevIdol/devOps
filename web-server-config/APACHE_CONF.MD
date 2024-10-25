# Installing Apache2 on Ubuntu and Creating a Virtual Host

## Step 1: Install Apache2

```sh
# Update package list
sudo apt update

# Install Apache2
sudo apt install apache2 -y
```

---

## Step 2: Create the Document Root

Create a directory for your domain where the website files will be stored:

```sh
sudo mkdir -p /var/www/domain.com/public_html
```

---

## Step 3: Set Permissions

Adjust permissions to ensure that Apache can access the website content:

```sh
sudo chown -R $USER:$USER /var/www/domain.com/public_html
sudo chmod -R 755 /var/www
```

---

## Step 4: Create an Apache Virtual Host File

Copy the default configuration file to create a new virtual host file:

```sh
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/domain.com.conf
```

---

## Step 5: Edit the Virtual Host File

Open the newly created configuration file in a text editor:

```sh
sudo nano /etc/apache2/sites-available/domain.com.conf
```

Modify it to match the following setup, adjusting `ServerAdmin`, `ServerName`, `ServerAlias`, and `DocumentRoot` as needed:

```apache
<VirtualHost *:80>
    ServerAdmin webmaster@domain.com
    ServerName domain.com
    ServerAlias www.domain.com
    DocumentRoot /var/www/domain.com/public_html

    ErrorLog ${APACHE_LOG_DIR}/domain-error.log
    CustomLog ${APACHE_LOG_DIR}/domain-access.log combined
</VirtualHost>
```

---

## Step 6: Enable the Virtual Host

Enable the new site configuration using `a2ensite`:

```sh
sudo a2ensite domain.com.conf
```

---

## Step 7: Disable the Default Site (Optional)

If you want to prevent interference from the default site, disable it with:

```sh
sudo a2dissite 000-default.conf
```

---

## Step 8: Reload Apache

Reload Apache to apply the changes:

```sh
sudo systemctl reload apache2
```

---

## Step 9: Install SSL with Certbot

Install Certbot and the Apache plugin for SSL:

```sh
sudo apt install certbot python3-certbot-apache
```

To obtain an SSL certificate, run the following command:

```sh
sudo certbot --apache
# or specify a domain directly
sudo certbot --apache -d domain.com -d www.domain.com
```

You will be prompted to enter your email address and agree to the terms of service. Certbot will then automatically obtain and configure the SSL certificate for your domain.

---

## Step 10: Verify SSL Certificate

To verify the SSL certificate installation:

```sh
# List certificates in the directory
sudo ls -lah /etc/letsencrypt/live/domain.com/

# View the updated Apache configuration file
cat /etc/apache2/sites-available/domain.com.conf
```

---

This completes the setup for an Apache virtual host with SSL on an Ubuntu server.
