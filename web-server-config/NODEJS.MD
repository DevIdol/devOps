# Manual Deployment of a Node.js Project on Ubuntu Server

## Step 1: Install Node.js

```sh
# Update package list
sudo apt update

# Install the latest LTS version of Node.js
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt install -y nodejs

# Alternatively, install Node.js 18 specifically
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs

# Handle any broken dependencies
sudo apt remove libnode-dev
sudo apt --fix-broken install
sudo apt install -y nodejs

# Verify installation
node --version
npm --version
```

---

## Step 2: Set Up and Build Project in `/var/www`

### Set Up Project Directory

```sh
# Navigate to /var/www directory
cd /var/www

# Set ownership and permissions for the project directory
sudo chown -R ubuntu:www-data .
sudo usermod -aG www-data ubuntu

# Clone your project repository (replace with your repository URL)
git clone [nodejs-project_repo]

# Navigate into the project folder
cd /var/www/nodejs-project
sudo chown -R ubuntu:www-data .
```

### Install PM2 and Configure the Application

```sh
# Install PM2 globally
sudo npm install -g pm2

# Create PM2 configuration file
nano ecosystem.config.js
```

Add the following code to `ecosystem.config.js`:

```js
module.exports = {
  apps: [
    {
      name: "next-app",
      script: "npm",
      args: "start -p 3000",
      watch: true,
      env: {
        NODE_ENV: "production",
      },
    },
  ],
};
```

---

## Step 3: Start and Configure PM2

```sh
# Start all PM2 applications
pm2 start

# List all running applications
pm2 ls

# Create a startup script for system reboots
pm2 startup

# Follow the instructions to run a command similar to:
sudo env PATH=$PATH:/usr/bin /usr/local/lib/node_modules/pm2/bin/pm2 startup systemd -u ubuntu --hp /home/ubuntu

# Save PM2 application state for auto-restart
pm2 save

# Optional PM2 Commands:
# pm2 stop all        # Stop all apps
# pm2 delete [name]   # Delete a specific app by name
```

---

## Step 4: Configure Nginx as a Reverse Proxy

### Create Nginx Configuration File

```sh
sudo nano /etc/nginx/conf.d/nodejs-project.conf
```

Add the following configuration:

```nginx
server {
    listen 80;
    server_name domain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Link the configuration file to `sites-enabled`:

```sh
sudo ln -s /etc/nginx/conf.d/nodejs-project.conf /etc/nginx/sites-enabled/
```

---

## Step 5: Test and Reload Nginx

### Test Nginx Configuration

```sh
sudo nginx -t
```

If there are no errors, reload Nginx to apply changes:

```sh
sudo nginx -s reload
# or
sudo systemctl reload nginx
# or
sudo systemctl restart nginx
```

---

This setup completes the deployment of a Node.js project on an Ubuntu server, with PM2 for process management and Nginx as a reverse proxy.
