# Nginx Installation and Virtual Host Setup on Ubuntu

## Step 1: Stop Apache2 (if installed)

```sh
sudo systemctl stop apache2
sudo systemctl disable apache2
sudo apt remove apache2
```

## Step 2: Install Nginx and Enable It

```sh
sudo apt update
sudo apt install nginx
sudo systemctl enable --now nginx
sudo systemctl status nginx
```

## Step 3: Test Nginx Installation

```sh
curl -i localhost

#sudo ufw status
#sudo ufw allow 'Nginx Full'
#sudo ufw allow 'Nginx HTTPS'
```

## Step 4: Verify Nginx Configuration

```sh
cat /etc/nginx/nginx.conf

# Virtual Host Configurations
include /etc/nginx/conf.d/*.conf;
include /etc/nginx/sites-enabled/*;
```

---

## Step 5: Create a Virtual Host

### Option 1: Add Configuration in `/etc/nginx/conf.d`

Create a configuration file:

```sh
sudo nano /etc/nginx/conf.d/my-project.conf
```

Add the following config:

```nginx
server {
    listen 80;
    server_name 34.155.133.104;
    root /var/www/my-project;
    index index.php index.html index.htm;
}
```

### Option 2: Add Configuration in `/etc/nginx/sites-available`

Create a configuration file:

```sh
sudo nano /etc/nginx/sites-available/my-project
```

Add the following config:

```nginx
server {
    listen 80;
    server_name example.com www.example.com;
    root /var/www/my-project;
    index index.php index.html index.htm;
}
```

Link the configuration file to `sites-enabled`:

```sh
sudo ln -s /etc/nginx/sites-available/my-project /etc/nginx/sites-enabled/
```

## Step 6: Test and Reload Nginx

```sh
sudo nginx -t
```

If no errors are shown, reload Nginx:

```sh
sudo nginx -s reload
# or
sudo systemctl reload nginx
# or
sudo systemctl restart nginx
```

---

## Step 7: Basic Authentication

### Add Basic Authentication to the Virtual Host

In your Nginx config, add the following to restrict access to `/admin`:

```nginx
server {
    # Existing server config...

    # Basic Authentication
    location /admin {
        auth_basic "Restricted Area";
        auth_basic_user_file /etc/nginx/.htpasswd;
    }
}
```

### Create the Password File

```sh
sudo apt install apache2-utils
sudo htpasswd -c /etc/nginx/.htpasswd admin

# Verify contents
cat /etc/nginx/.htpasswd
```

---

## Step 8: Configure Directory Permissions

### Check Directory Permissions

```sh
ll -h /var/www/my-project/
```

Example output:

```plaintext
total 12K
drwxr-xr-x 2 root root 4.0K Oct 23 15:04 ./
drwxr-xr-x 4 root root 4.0K Oct 23 15:03 ../
-rw-r--r-- 1 root root  249 Oct 23 15:04 index.html
```

### Add Ubuntu User to `www-data` Group

```sh
sudo usermod -aG www-data ubuntu
```

### Change Ownership to `ubuntu:www-data`

```sh
sudo chown -R ubuntu:www-data /var/www/my-project
```

### Verify Permissions Again

```sh
ll -h /var/www/my-project/
```

Example output:

```plaintext
total 16
drwxr-xr-x 3 ubuntu www-data 4096 Oct 23 15:52 ./
drwxr-xr-x 4 root   root     4096 Oct 23 15:35 ../
drwxr-xr-x 2 ubuntu www-data 4096 Oct 23 15:52 admin/
-rw-r--r-- 1 ubuntu www-data  249 Oct 23 15:36 index.html
```

---

## Step 9: SSL Installation with Certbot

```sh
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx
```

To specify a domain:

```sh
sudo certbot --nginx -d example.com
```

### Check Certificate

```sh
sudo ls -lah /etc/letsencrypt/live/example.com/
```

## Step 10: SSL Auto-Renewal with Cron Job

```sh
sudo crontab -e
```

Add the following line to renew the SSL certificate monthly:

```sh
0 0 1 * * certbot renew --quiet --nginx > /var/log/letsencrypt-renew.log 2>&1
```

---

## Step 11: Nginx Logs and Log Rotation

### Access Nginx Logs

```sh
cd /var/log/nginx
ll
cat access.log
tail -n 10 access.log
tail -n 10 -f access.log  # Stream logs
```

### Filter Logs for Specific Endpoints

```sh
cat access.log | grep admin
grep "/admin" access.log
```

### Set Up Log Rotation

To ensure logs do not grow too large, set up log rotation.

```sh
logrotate --version
sudo apt install logrotate
cd /etc/logrotate.d/
cat nginx
```

---

## Step 12: Backup Nginx Service and Stop It

```sh
cp /lib/systemd/system/nginx.service ~/
sudo systemctl stop nginx
sudo systemctl disable nginx
sudo systemctl status nginx
```

---

### To Enable UFW and Configure Basic Rules

If you’d like to activate UFW and set up basic firewall rules, here are some recommended steps:

1. **Enable UFW**:

   ```sh
   sudo ufw enable
   ```

2. **Allow SSH Connections**:

   - Allowing SSH on port 22 ensures you don’t lose remote access once the firewall is enabled:
     ```sh
     sudo ufw allow ssh
     ```
   - Alternatively, you can specify the port explicitly:
     ```sh
     sudo ufw allow 22
     ```

3. **Allow HTTP and HTTPS Connections** (if running a web server):

   ```sh
   sudo ufw allow 80   # For HTTP
   sudo ufw allow 443  # For HTTPS
   ```

4. **Check UFW Status**:
   - To verify which rules are currently active:
     ```sh
     sudo ufw status
     ```

After configuring these rules, your UFW firewall will be active, allowing only the specified ports, which enhances server security. Let me know if you need help with any other specific configurations!
