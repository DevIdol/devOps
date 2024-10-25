# Installing Apache2 on Ubuntu and creating a virtual host

## Installation

```sh
sudo apt udpate
sudo apt install apache2 -y
```

## Create the Document Root

Create a directory for your domain where the website files will live:

```sh
sudo mkdir -p /var/www/domain.com/public_html
```

## Set Permission

Adjust the permissions to ensure that the web content can be accessed:

```sh
sudo chown -R $USER:$USER /var/www/domain.com/public_html
sudo chmod -R 755 /var/www
```

## Create Apache Virtual Host File\*

Copy the default configuration file to create a new virtual host file:

```cmd
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/domain.com.conf
```

## Edit the Virtual Host File

Open the newly created configuration file in a text editor:

```sh
sudo nano /etc/apache2/sites-available/domain.com.conf

#check
ll -h /etc/apache2/sites-available/
```

Then, modify it to match the following setup, adjusting ServerAdmin, ServerName, ServerAlias, and DocumentRoot as needed:

```apache
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    ServerName domain.com
    ServerAlias www.domain.com
    DocumentRoot /var/www/domain.com/public_html
    ErrorLog ${APACHE_LOG_DIR}/devidol-error.log
    CustomLog ${APACHE_LOG_DIR}/devidol-access.log combined
</VirtualHost>
```

## Enable the Virtual Host

Enable the new site configuration using `a2ensite`:

```sh
sudo a2ensite domain.com.conf
```

## Disable the Default Site (Optional)

If you wish, disable the default site to prevent it from interfering:

```cmd
sudo a2dissite 000-default.conf
```

## Reload Apache

Reload Apache to apply the changes:

```cmd
sudo systemctl reload apache2
```

## SSL Installation
You can install SSL by using certbot. You can install certbot by running the following commands:

```sh
sudo apt install certbot python3-certbot-apache2
```
Then you can obtain a certificate by running the following command:

```sh
sudo certbot --apache2
#or
sudo certbot --apache2 -d my-project.com  ## my-project.com need to add DNS

## check ssl certificate
sudo ls -lah /etc/letsencrypt/live/my-project.com/

cat /etc/apache2/sites-available/my-project
```