# Manual Deployment Laravel Inertia Vue

## system requirements
- PHP >= 8.3
- Composer >= 2.1.9
- Node.js >= 18.0.0
- NPM >= 7.24.0
- MySQL >= 8.0.27
- PHP Extensions
  - BCMath
  - Ctype
  - Fileinfo
  - JSON
  - Mbstring
  - OpenSSL
  - PDO
  - Tokenizer
  - XML
  - GD
  - Zip
  - Exif
  - MySQL
  - Redis
  - Composer
  - MySQL


## Install php in Ubuntu

```sh
sudo apt update

sudo apt install software-properties-common

sudo apt install -y php8.3 #if you can't install php8.3, sudo add-apt-repository ppa:ondrej/php && sudo apt-get update

php -v

sudo apt-get install -y php8.3-fpm php8.3-cli php8.3-common php8.3-mbstring php8.3-gd php8.3-intl php8.3-xml php8.3-mysql php8.3-zip php8.3-bcmath php8.3-soap php8.3-redis php8.3-curl php8.3-imagick php8.3-xmlrpc php8.3-xsl

curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer

sudo apt install -y mysql-server mysql-client

sudo mysql_secure_installation

sudo mysql
```

## Create user and database
```mysql
CREATE USER 'laravel'@'localhost' IDENTIFIED BY 'root';

CREATE DATABASE laravel;

GRANT ALL PRIVILEGES ON laravel.* TO 'laravel'@'localhost';

FLUSH PRIVILEGES;
```

## Change the user and password
```msql
ALTER USER 'username'@'localhost' IDENTIFIED BY 'new_password';

#or
SET PASSWORD FOR 'username'@'localhost' = 'new_password';

CREATE USER 'new_username'@'localhost' IDENTIFIED BY 'password';

GRANT ALL PRIVILEGES ON *.* TO 'new_username'@'localhost';

DROP USER 'old_username'@'localhost';

```

## Install Nodejs in Ubuntu Server
```sh
sudo apt update

curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -

sudo apt install -y nodejs

# with version
# sudo apt remove nodejs
# sudo apt autoremove
# sudo apt clean


curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -

sudo apt install -y nodejs

sudo apt remove libnode-dev

sudo apt --fix-broken install

sudo apt install -y nodejs

node --version
npm --version
```

## Clone Source Code into `/var/www/`
```sh
cd /var/www/

ll

sudo chown -R ubuntu:www-data .

sudo usermod -aG www-data ubuntu

cd /var/www/laravel

sudo chown -R ubuntu:www-data .

```

## Build Project
Install PHP dependencies specified in the composer.json file.
```sh
composer install

# Install JavaScript dependencies specified in the package.json file.
npm install

# Build the assets (JavaScript and CSS) for the production environment.
npm run build

cp .env.example .env

nano .env # edit database configuration

# Generate a new application key for Laravel, which is used for encrypting session data and other secure information.
php artisan key:generate

# Run the database migrations to create the necessary tables in the database.
php artisan migrate

# Seed the database with initial data defined in the database seeders.
php artisan db:seed

# Optimize the application for better performance (caching configurations, routes, etc.).
php artisan optimize
```


## Nginx Configuration

```nginx
server {
    listen 80;
    server_name domain.com;
    root /var/www/laravel/public;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.php index.html index.htm index.nginx-debian.html;

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

## php-fpm Configuration

```sh
nano /etc/php/8.3/fpm/pool.d/www.conf
```

```nginx
user = www-data
group = www-data
listen = /var/run/php/php8.3-fpm.sock
listen.owner = www-data
listen.group = www-data
listen.mode = 0660
```

## check fpm service
```sh
sudo systemctl status php8.3-fpm
```