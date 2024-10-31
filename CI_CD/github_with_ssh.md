# Deploy to Server using GitHub Actions

## Prerequisites

Before using this workflow, ensure you have the following:

1. **Server Access**: You must have a server (e.g., AWS EC2, DigitalOcean, etc.) where you want to deploy your application.

2. **SSH Access**: Ensure that you can connect to your server via SSH using a private key.

3. **SSH Key**: Generate an SSH key pair if you do not have one. You can generate it using the following command:

   ```bash
   ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
   ```

   Ensure that you add the public key (usually found in `~/.ssh/id_rsa.pub`) to the `~/.ssh/authorized_keys` file on your server.

4. **GitHub Repository Secrets**: Store your server credentials as secrets in your GitHub repository:

   - `HOST`: The IP address or hostname of your server.
   - `USERNAME`: The username used to SSH into your server.
   - `SSHKEY`: Your private SSH key (ensure this is properly formatted and does not contain extra newlines).

   To add secrets, navigate to your repository on GitHub, go to **Settings** > **Secrets and variables** > **Actions**, and click **New repository secret**.

## Workflow Configuration

Create a new file in your repository at `.github/workflows/deploy_prod.yml` and add the following code:

```yaml
name: Deploy to Prod

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: Deploy To Server
        uses: appleboy/ssh-action@v1.1.0
        with:
          host: ${{ secrets.HOST }}
          username: ${{ secrets.USERNAME }}
          key: ${{ secrets.SSHKEY }}
          script: "cd /home/ubuntu/devops && ./deploy.sh"
```

## Usage

1. Make changes to your codebase and push them to the `main` branch of your GitHub repository.
2. The GitHub Actions workflow will automatically trigger, checking out your code and deploying it to your server.

## Important Notes

- Ensure that your `deploy.sh` script is executable. You can set the executable permission with the following command on your server:
  ```bash
  chmod +x /home/ubuntu/devops/deploy.sh
  ```

## `deploy.sh`: Deployment Script for Laravel Inertia (Vue)

```sh
#!/bin/bash

# Exit immediately if a command exits with a non-zero status.
set -e

# Set the project directory
PROJECT_DIR="/var/www/laravel-project"

# Navigate to the project directory
cd $PROJECT_DIR

# Pull the latest changes from the repository
echo "Pulling latest changes from the repository..."
git pull origin main

# Install Composer dependencies
echo "Installing Composer dependencies..."
composer install --no-interaction --prefer-dist --optimize-autoloader

# Install Node.js dependencies
echo "Installing Node.js dependencies..."
npm install

# Build the assets
echo "Building assets..."
npm run build

# Run database migrations
echo "Running database migrations..."
php artisan migrate --force

# Clear the application, config, and route cache
echo "Clearing application, config, and route cache..."
php artisan optimize

# Create necessary directories and set permissions
echo "Creating necessary directories and setting permissions..."
sudo mkdir -p bootstrap/cache storage/framework/{cache,sessions,views}
sudo chmod -R 775 bootstrap/cache storage
sudo chown -R www-data:www-data bootstrap/cache storage

# Create cache data directory if it doesn't exist
sudo mkdir -p /var/www/laravel-project/storage/framework/cache/data
sudo chown -R www-data:www-data /var/www/laravel-project/storage
sudo chmod -R 775 /var/www/laravel-project/storage

# Ensure correct ownership and permissions for storage and bootstrap/cache
sudo chown -R www-data:www-data /var/www/laravel-project/storage /var/www/laravel-project/bootstrap/cache
sudo chmod -R 775 /var/www/laravel-project/storage /var/www/laravel-project/bootstrap/cache

# Restart the queue workers (if you're using Laravel queues)
# echo "Restarting queue workers..."
# php artisan queue:restart

# Exit the script
echo "Deployment completed successfully!"
exit 0

```
