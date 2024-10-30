# Manual Deployment for Laravel Inertia Vue

## System Requirements

- **PHP** >= 8.3
- **Composer** >= 2.1.9
- **Node.js** >= 18.0.0
- **NPM** >= 7.24.0
- **MySQL** >= 8.0.27
- **PHP Extensions**: BCMath, Ctype, Fileinfo, JSON, Mbstring, OpenSSL, PDO, Tokenizer, XML, GD, Zip, Exif, MySQL, Redis

---

## Step 1: Install PHP on Ubuntu

```sh
sudo apt update
sudo apt install software-properties-common
sudo apt install -y php8.3 # If unable to install, run `sudo add-apt-repository ppa:ondrej/php && sudo apt update`
php -v

# Install necessary PHP extensions
sudo apt-get install -y php8.3-fpm php8.3-cli php8.3-common php8.3-mbstring php8.3-gd php8.3-intl php8.3-xml php8.3-mysql php8.3-zip php8.3-bcmath php8.3-soap php8.3-redis php8.3-curl php8.3-imagick php8.3-xmlrpc php8.3-xsl
```

Install Composer:

```sh
curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer
```

Install MySQL:

```sh
sudo apt install -y mysql-server mysql-client
sudo mysql_secure_installation
sudo mysql
```

---

## Step 2: Create a MySQL User and Database

```mysql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'root'; 
## CREATE USER 'laravel'@'localhost' IDENTIFIED BY 'root';

CREATE DATABASE laravel;
GRANT ALL PRIVILEGES ON laravel.* TO 'root'@'localhost';
FLUSH PRIVILEGES;
```

---

## Step 3: Changing User Passwords (Optional)

```mysql
ALTER USER 'username'@'localhost' IDENTIFIED BY 'new_password';
## ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'new_password';
SET PASSWORD FOR 'username'@'localhost' = 'new_password';
CREATE USER 'new_username'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON *.* TO 'new_username'@'localhost';
DROP USER 'old_username'@'localhost';
```

---

## Step 4: Install Node.js on Ubuntu Server

```sh
sudo apt update
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt install -y nodejs

# Alternatively, install Node.js 18
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs

# Fix broken dependencies if needed
sudo apt remove libnode-dev
sudo apt --fix-broken install
```

Check versions:

```sh
node --version
npm --version
```

---

## Step 5: Clone the Source Code into `/var/www/`

```sh
cd /var/www/
sudo chown -R ubuntu:www-data .
sudo usermod -aG www-data ubuntu

# Clone the repository
cd /var/www/laravel
sudo chown -R ubuntu:www-data .
```

---

## Step 6: Build the Project

Copy `.env` file and configure:

```sh
cp .env.example .env
nano .env # Edit database configuration
```

Install JavaScript dependencies:

```sh
npm install
```

Build assets for production:

```sh
npm run build
```

Install PHP dependencies:

```sh
composer install
```

Generate a Laravel application key:

```sh
php artisan key:generate
```

Run migrations and seed data:

```sh
php artisan migrate
php artisan db:seed
```

Optimize application performance:

```sh
php artisan optimize
```

---

## Step 7: Configure Nginx

Edit Nginx configuration file:

```sh
sudo nano /etc/nginx/sites-available/my-project
```

Add the following configuration:

```nginx
server {
    listen 80;
    server_name domain.com;
    root /var/www/laravel/public;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.php index.html index.htm;

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

Enable the configuration:

```sh
sudo ln -s /etc/nginx/sites-available/my-project /etc/nginx/sites-enabled/
```

---

## Step 8: Test and Reload Nginx

Test the configuration:

```sh
sudo nginx -t
```

If successful, reload Nginx:

```sh
sudo nginx -s reload
# or
sudo systemctl reload nginx
```

---

## Step 9: Configure Apache (Optional)

For Apache users, create a virtual host file:

```apache
<VirtualHost *:80>
    ServerName domain.com
    DocumentRoot /var/www/laravel/public
    ErrorLog ${APACHE_LOG_DIR}/domain-error.log
    CustomLog ${APACHE_LOG_DIR}/domain-access.log combined

    <Directory /var/www/laravel/public>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    <FilesMatch "^\.">
        Require all denied
    </FilesMatch>

    Options -Indexes

    Header set X-Frame-Options "SAMEORIGIN"
    Header set X-XSS-Protection "1; mode=block"
    Header set X-Content-Type-Options "nosniff"

    ErrorDocument 404 /index.php

    <FilesMatch \.php$>
        SetHandler application/x-httpd-php
    </FilesMatch>
</VirtualHost>
```

---

## Step 10: Configure PHP-FPM

Edit the PHP-FPM configuration file:

```sh
sudo nano /etc/php/8.3/fpm/pool.d/www.conf
```

Adjust these settings:

```nginx
user = www-data
group = www-data
listen = /var/run/php/php8.3-fpm.sock
listen.owner = www-data
listen.group = www-data
listen.mode = 0660
```

Check PHP-FPM status:

```sh
sudo systemctl status php8.3-fpm
```

---

This completes the setup for manually deploying a Laravel Inertia Vue application on an Ubuntu server.

---

## Step 11: Configure Supervisor (Optional)

If you need to manage Laravel queue workers, install Supervisor:

```sh
sudo apt install supervisor -y
```

Create a Supervisor configuration file for your Laravel application:

```sh
sudo nano /etc/supervisor/conf.d/laravel-worker.conf
```

Add the following configuration:

```ini
[program:laravel-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /var/www/laravel/artisan queue:work --sleep=3 --tries=3
autostart=true
autorestart=true
user=www-data
numprocs=1
redirect_stderr=true
stdout_logfile=/var/www/laravel/storage/logs/worker.log
```

Update Supervisor:

```sh
sudo supervisorctl reread
sudo supervisorctl update
sudo supervisorctl start laravel-worker:*
```

Check the status of Supervisor processes:

```sh
sudo supervisorctl status
```

## Troubleshooting Commands

**Ensure Permissions on Key Directories:**

```sh
sudo mkdir -p bootstrap/cache storage/framework/{cache,sessions,views}
sudo chmod -R 775 bootstrap/cache storage
sudo chown -R www-data:www-data bootstrap/cache storage

ls -l /var/www/pmm/storage/framework/cache/data
sudo mkdir -p /var/www/pmm/storage/framework/cache/data
sudo chown -R www-data:www-data /var/www/pmm/storage
sudo chmod -R 775 /var/www/pmm/storage

sudo chown -R www-data:www-data /var/www/pmm/storage /var/www/pmm/bootstrap/cache
sudo chmod -R 775 /var/www/pmm/storage /var/www/pmm/bootstrap/cache

```

**Refresh Application Cache:**

```sh
php artisan config:cache
php artisan route:cache
php artisan view:cache
php artisan optimize
```

---