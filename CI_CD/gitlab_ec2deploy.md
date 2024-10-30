# Laravel AWS EC2 Deployment with GitLab CI/CD

This guide provides steps for setting up a GitLab CI/CD pipeline to deploy a Laravel application to an AWS EC2 instance.

---

## Prerequisites

1. **AWS EC2 Setup:**
   - Make sure your EC2 instance is set up with SSH access.
   - Your Laravel app directory should be in `/var/www/laravel-todo` or your specified `$APP_DIR`.
2. **Environment Variables**:
   - Add `PROD_HOST` (your EC2 public IP or hostname) and `SSH_PRIVATE_KEY` (your private SSH key for deployment) as GitLab CI/CD **Project Variables**.

---

## GitLab CI/CD Pipeline Configuration

Save this `.gitlab-ci.yml` in the root of your Laravel project.

```yml
image: php:8.3-alpine

# Define pipeline stages
stages:
  - build # Stage for building the application
  - test # Stage for running tests on the application
  - deploy # Stage for deploying the application to production

# Define reusable variables
variables:
  USER: "ubuntu" # EC2 username, replace if different
  APP_DIR: "/var/www/laravel-todo" # Directory on EC2 to deploy Laravel app

# Before Script to set up SSH
before_script:
  - apk add --no-cache openssh-client # Install SSH client
  - mkdir -p ~/.ssh # Create SSH directory
  - echo "$SSH_PRIVATE_KEY" | tr -d '\r' > ~/.ssh/id_ed25519 # Add private key
  - chmod 600 ~/.ssh/id_ed25519 # Set permissions
  - '[[ -f /.dockerenv ]] && echo -e "Host *\n\tStrictHostKeyChecking no\n\n" > ~/.ssh/config' # SSH config

# Cache Composer dependencies
cache:
  paths:
    - ~/.composer/cache # Cache Composer cache directory
    - vendor # Cache vendor directory for PHP dependencies
    - node_modules # Cache Node.js dependencies

# Build Job
build-prod:
  stage: build # Build stage
  when: manual # Run manually for controlled releases
  script:
    - echo "Building the application..."
    - curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer # Install Composer
    - composer install --no-dev --optimize-autoloader # Install dependencies
    - apk add --no-cache nodejs npm # Install Node.js and npm
    - npm install # Install frontend dependencies
    - npm run build # Build frontend assets
  artifacts:
    paths:
      - vendor # Cache vendor for faster deployments
      - node_modules # Cache Node.js modules
      - public # Cache public directory
  only:
    - main # Run only on the main branch

# Test Job
test-prod:
  stage: test # Test stage
  when: on_success # Run only if build succeeds
  needs: ["build-prod"] # Use artifacts from the build stage
  script:
    - echo "Running tests..."
    - mkdir -p tests/Feature
    - curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer # Install Composer
    - cp .env.example .env # Copy example env
    - php artisan key:generate # Generate application key
    - sed -i 's/DB_CONNECTION=mysql/DB_CONNECTION=sqlite/g' .env # Use SQLite for testing
    - composer install # Install dependencies for testing
    - php artisan migrate --force # Run migrations
    - php artisan db:seed # Seed the database
    - php artisan test # Run tests
  only:
    - main # Run only on the main branch

# Deploy Job
deploy-prod:
  stage: deploy
  when: manual # Run manually for controlled releases
  image: alpine:latest # Lightweight environment for SSH commands
  script:
    - echo "Deploying to production..."
    - ssh $USER@$PROD_HOST "cd $APP_DIR && php artisan down" # Put the app in maintenance mode
    - ssh $USER@$PROD_HOST "cd $APP_DIR && git reset --hard HEAD" # Reset local changes to match the latest commit
    - ssh $USER@$PROD_HOST "cd $APP_DIR && git pull origin main" # Pull latest code
    - ssh $USER@$PROD_HOST "cd $APP_DIR && npm install" # Install frontend dependencies
    - ssh $USER@$PROD_HOST "cd $APP_DIR && npm run build" # Build frontend assets
    - ssh $USER@$PROD_HOST "cd $APP_DIR && composer install --no-dev --optimize-autoloader" # Install optimized PHP dependencies
    - ssh $USER@$PROD_HOST "cd $APP_DIR && php artisan optimize:clear" # Clear any old cache
    - ssh $USER@$PROD_HOST "cd $APP_DIR && php artisan migrate --force" # Run migrations
    - ssh $USER@$PROD_HOST "cd $APP_DIR && php artisan up" # Bring the app back online
    - ssh $USER@$PROD_HOST "cd $APP_DIR && php artisan optimize" # Optimize the application cache
    - ssh $USER@$PROD_HOST "cd $APP_DIR && php artisan event:cache" # Cache events
  only:
    - main # Run only on the main branch
```

---

## Explanation of Key Sections

- **Variables**:

  - `$USER` - The username for SSH on your EC2 instance.
  - `$APP_DIR` - Directory where your Laravel app will be deployed on EC2.

- **Stages**:

  - `build-prod` - Installs dependencies, builds assets, and prepares files for deployment.
  - `test-prod` - Runs application tests to verify successful build integrity.
  - `deploy-prod` - Deploys the built application to EC2 using SSH commands.

- **SSH Configuration**:
  - Adds and configures the private SSH key from GitLab’s environment variables.
  - Disables strict host key checking for easier SSH access in CI/CD.

---
