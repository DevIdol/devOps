## Installing Nginx on Ubuntu and creating a virtual host

### stop apache2
```sh
sudo systemctl stop apache2

sudo systemctl disable apache2

sudo apt remove apache2

```

### install nginx and enable
```sh
sudo apt update

sudo apt install nginx

sudo systemctl enable --now nginx

sudo systemctl status nginx

```

### test nginx server
```sh
curl -i localhost
```

### check nginx conf
```sh
cat /etc/nginx/nginx.conf

## Virtual Host Configs
	include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;
```


## Creating a virtual host
You can create a virtual host by creating a new configuration file in the `/etc/nginx/sites-available` directory. For example, you can create a file called `my-project` by running the following command:
```sh
sudo nano /etc/nginx/conf.d/my-project.conf
```
Then you can add the following configuration to the file:
```nginx
server {
    listen 80;
    server_name 34.155.133.104;
    root /var/www/my-project;
    index index.php index.html index.htm;
 }
```
### OR

```sh
sudo nano /etc/nginx/sites-available/my-project
```
Then you can add the following configuration to the file:
```nginx
server {
    listen 80;
    server_name example.com www.example.com;
    root /var/www/my-project;
    index index.php index.html index.htm;
 }
```

After creating the configuration file, you need to create a symbolic link to the `/etc/nginx/sites-enabled` directory by running the following command:
```sh
sudo ln -s /etc/nginx/sites-available/my-project /etc/nginx/sites-enabled/
```
Then you can test the configuration by running the following command:

```sh
sudo nginx -t
```

If the configuration is correct, you can reload Nginx by running the following command:

```sh
sudo nginx -s reload
#or
sudo systemctl reload nginx
#or
sudo systemctl restart nginx
```


## Basic Authentication
You can add basic authentication to your virtual host's custom pages by creating a password file and adding the following configuration to the virtual host's configuration file:

```nginx
server {

    ## ....

    # Basic Authentication to admin directory
    location /admin {
        auth_basic "Restricted Area";
        auth_basic_user_file /etc/nginx/.htpasswd;
    }
}
```
You need to install and can create a password file by running the following command:

```bash
sudo apt install apache2-utils
sudo htpasswd -c /etc/nginx/.htpasswd admin

## check
cat /etc/nginx/.htpasswd
```


### Check permission
```sh
ll -h /var/www/my-project/

#example
total 12K
drwxr-xr-x 2 root root 4.0K Oct 23 15:04 ./
drwxr-xr-x 4 root root 4.0K Oct 23 15:03 ../
-rw-r--r-- 1 root root  249 Oct 23 15:04 index.html
```

### Change ubuntu user to www-data group
```sh
sudo usermod -aG www-data ubuntu
```
### chage ownwership 
```sh
sudo chown -R ubuntu:www-data .
```


### Check again permission
```sh
ll -h /var/www/my-project/

#example
total 16
drwxr-xr-x 3 ubuntu www-data 4096 Oct 23 15:52 ./
drwxr-xr-x 4 root   root     4096 Oct 23 15:35 ../
drwxr-xr-x 2 ubuntu www-data 4096 Oct 23 15:52 admin/
-rw-r--r-- 1 ubuntu www-data  249 Oct 23 15:36 index.html
```


## SSL Installation
You can install SSL by using certbot. You can install certbot by running the following commands:

```sh
sudo apt install certbot python3-certbot-nginx
```

Then you can obtain a certificate by running the following command:

```sh
sudo certbot --nginx
#or
sudo certbot --nginx -d my-project.com  ## my-project.com need to add DNS

## check ssl certificate
sudo ls -lah /etc/letsencrypt/live/my-project.com/

cat /etc/nginx/sites-available/my-project
```

You will be prompted to enter your email address and agree to the terms of service. After that, certbot will automatically obtain and install a certificate for your domain.

check certbot timer
```sh
sudo systemctl list-timers

cat /lib/systemd/system/certbot.timer
```

## SSL Auto-Renewal
You can set up auto-renewal for your SSL certificate by creating a cron job. You can do this by running the following command:

```sh
sudo crontab -e
```

Then you can add the following line to the crontab file: to renew the certificate every month

```bash
0 0 1 * * certbot renew --quiet --force-renewal --nginx > /var/log/letsencrypt-renew.log 2>&1
# 0 0 1 is min hour day
#check
sudo certbot renew --nginx
```


## Nninx Logs

```sh
cd /var/log/nginx

ll

cat access.log

tail -n 10 access.log

tail -n 10 -f access.log ## streaming

cat access.log | grep admin

grep admin access.log

grep "/admin" access.log
```

## Logrotate
 - [Reference](https://www.digitalocean.com/community/tutorials/how-to-configure-logging-and-log-rotation-in-nginx-on-an-ubuntu-vps)
```sh
logrotate --version

sudo apt install logrotate

cd /etc/logrotate.d/

cat nginx

ll -h /var/log/nginx


```