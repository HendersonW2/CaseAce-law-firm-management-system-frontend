# CaseAce Frontend - Installation Guide for AlmaLinux 9

**Optimized for:** AlmaLinux 9 | 2 CPUs | 4GB RAM | 110GB Storage

---

## Table of Contents
1. [System Requirements](#system-requirements)
2. [Step 1: Initial Server Setup](#step-1-initial-server-setup)
3. [Step 2: Install PHP & Web Server](#step-2-install-php--web-server)
4. [Step 3: Install Node.js & npm](#step-3-install-nodejs--npm)
5. [Step 4: Install Composer](#step-4-install-composer)
6. [Step 5: Clone Repository](#step-5-clone-repository)
7. [Step 6: Install Dependencies](#step-6-install-dependencies)
8. [Step 7: Configure Environment](#step-7-configure-environment)
9. [Step 8: Setup Web Server](#step-8-setup-web-server)
10. [Step 9: Test Installation](#step-9-test-installation)
11. [Troubleshooting](#troubleshooting)

---

## System Requirements

✅ **Your System Specs:**
- OS: AlmaLinux 9
- CPUs: 2
- RAM: 4GB
- Storage: 110GB (plenty for this application)

✅ **Required Software (to be installed):**
- PHP 8.0+ with modules (curl, json, fpm)
- Nginx web server
- Composer (PHP dependency manager)
- Node.js & npm
- Git

---

## Step 1: Initial Server Setup

### 1.1 Update System

```bash
# Update system packages
sudo dnf update -y

# Upgrade all packages
sudo dnf upgrade -y

# Install essential tools
sudo dnf install -y git wget curl vim nano htop
```

**Expected time:** 2-5 minutes

### 1.2 Disable SELinux (Recommended for Development)

```bash
# Check SELinux status
getenforce

# Disable SELinux temporarily
sudo setenforce 0

# Permanently disable (edit file)
sudo nano /etc/selinux/config
```

Change `SELINUX=enforcing` to `SELINUX=disabled`, then save (Ctrl+O, Enter, Ctrl+X)

```bash
# Reboot to apply changes
sudo reboot
```

### 1.3 Create Application User (Optional but Recommended)

```bash
# Create non-root user for the application
sudo useradd -m -s /bin/bash caseace

# Add user to sudoers group
sudo usermod -aG wheel caseace

# Switch to the new user
su - caseace

# Or login as caseace directly
ssh caseace@your_server_ip
```

---

## Step 2: Install PHP & Web Server

### 2.1 Install PHP 8.0

```bash
# Enable PowerTools repository (required for PHP 8.0)
sudo dnf config-manager --set-enabled powertools

# Install PHP 8.0 and required modules
sudo dnf install -y php php-cli php-fpm php-common php-curl php-json php-mbstring php-pdo php-xml php-gd php-zip

# Verify PHP installation
php --version
```

**Expected output:** `PHP 8.0.x (cli) ...`

### 2.2 Install Nginx Web Server

```bash
# Install Nginx
sudo dnf install -y nginx

# Start Nginx
sudo systemctl start nginx

# Enable Nginx to start on boot
sudo systemctl enable nginx

# Check Nginx status
sudo systemctl status nginx
```

**Expected output:** `active (running)`

### 2.3 Start PHP-FPM

```bash
# Start PHP-FPM service
sudo systemctl start php-fpm

# Enable PHP-FPM to start on boot
sudo systemctl enable php-fpm

# Check PHP-FPM status
sudo systemctl status php-fpm
```

**Expected output:** `active (running)`

---

## Step 3: Install Node.js & npm

### 3.1 Install Node.js LTS

```bash
# Enable NodeJS repository
sudo dnf module enable nodejs:18

# Install Node.js (includes npm)
sudo dnf install -y nodejs

# Verify installations
node --version
npm --version
```

**Expected output:**
```
v18.x.x
9.x.x
```

---

## Step 4: Install Composer

### 4.1 Download and Install Composer

```bash
# Download Composer installer
curl -sS https://getcomposer.org/installer -o composer-setup.php

# Install Composer globally
sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer

# Verify installation
composer --version

# Remove installer
rm composer-setup.php
```

**Expected output:** `Composer version x.x.x`

---

## Step 5: Clone Repository

### 5.1 Navigate to Web Root

```bash
# Create web application directory (if not exists)
sudo mkdir -p /var/www/caseace

# Change ownership to nginx user
sudo chown -R nginx:nginx /var/www/caseace

# Change permissions
sudo chmod -R 755 /var/www/caseace
```

### 5.2 Clone Your Forked Repository

```bash
# Navigate to directory
cd /var/www/caseace

# Clone the repository
sudo git clone https://github.com/HendersonW2/CaseAce-law-firm-management-system-frontend.git .

# Verify files are present
ls -la
```

**Expected files:**
```
index.php
composer.json
package.json
components/
php/
css/
javascript/
assets/
```

### 5.3 Fix Permissions

```bash
# Change ownership to nginx user
sudo chown -R nginx:nginx /var/www/caseace

# Set directory permissions
sudo chmod -R 755 /var/www/caseace

# Set file permissions
sudo chmod -R 644 /var/www/caseace/*.{php,json}
```

---

## Step 6: Install Dependencies

### 6.1 Install PHP Dependencies (Composer)

```bash
# Navigate to project directory
cd /var/www/caseace

# Install dependencies with Composer
sudo -u nginx composer install --no-dev --optimize-autoloader

# Verify installation
ls -la vendor/
```

**Expected time:** 1-2 minutes  
**Expected output:** `autoload.php` file in vendor directory

### 6.2 Install Node.js Dependencies (npm)

```bash
# Install npm dependencies
sudo -u nginx npm install --production

# Verify installation
npm list axios
```

**Expected output:**
```
caseace_frontend@1.0.0
└── axios@1.6.2
```

**Total time:** 2-3 minutes for both

---

## Step 7: Configure Environment

### 7.1 Create .env File

```bash
# Navigate to project
cd /var/www/caseace

# Create .env file
sudo nano .env
```

### 7.2 Add Configuration (Copy & Paste)

```env
# Application Settings
APP_NAME=CaseAce
APP_ENV=production
APP_DEBUG=false

# Server Configuration
BASE_URL=http://your_domain.com/
API_BASE_URL=http://your_domain.com/api

# Database (MongoDB)
DB_HOST=localhost
DB_PORT=27017
DB_NAME=caseace_db
DB_USER=
DB_PASSWORD=

# File Upload Configuration
UPLOAD_DIR=/var/www/caseace/uploads/
MAX_FILE_SIZE=10485760

# Security
JWT_SECRET=your_secure_jwt_secret_key_change_this_12345
SESSION_TIMEOUT=3600

# Cloudinary Configuration (for document storage)
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret

# Backend API
BACKEND_URL=http://your_backend_domain.com/api
```

**To save:** Ctrl+O → Enter → Ctrl+X

### 7.3 Update Permissions for .env

```bash
# Change ownership
sudo chown nginx:nginx .env

# Restrict permissions (only owner can read)
sudo chmod 600 .env

# Verify
ls -la .env
```

### 7.4 Create Uploads Directory

```bash
# Create uploads directory
sudo mkdir -p /var/www/caseace/uploads

# Set permissions
sudo chown nginx:nginx /var/www/caseace/uploads
sudo chmod 755 /var/www/caseace/uploads
```

---

## Step 8: Setup Web Server

### 8.1 Create Nginx Configuration

```bash
# Create Nginx config file
sudo nano /etc/nginx/conf.d/caseace.conf
```

### 8.2 Add Nginx Configuration (Copy & Paste)

```nginx
server {
    listen 80;
    server_name your_domain.com www.your_domain.com;
    root /var/www/caseace;

    index index.php index.html index.htm;

    # Logs
    access_log /var/log/nginx/caseace_access.log;
    error_log /var/log/nginx/caseace_error.log;

    # Maximum upload size
    client_max_body_size 10M;

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    # PHP-FPM configuration
    location ~ \.php$ {
        include /etc/nginx/fastcgi_params;
        fastcgi_pass unix:/run/php-fpm/www.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }

    # Deny access to hidden files
    location ~ /\. {
        deny all;
        access_log off;
        log_not_found off;
    }

    # Deny access to vendor and node_modules
    location ~ ^/(vendor|node_modules)/ {
        deny all;
        access_log off;
        log_not_found off;
    }
}
```

**To save:** Ctrl+O → Enter → Ctrl+X

### 8.3 Test Nginx Configuration

```bash
# Test configuration syntax
sudo nginx -t
```

**Expected output:** `syntax is ok` and `test is successful`

### 8.4 Reload Nginx

```bash
# Reload Nginx with new configuration
sudo systemctl reload nginx

# Verify status
sudo systemctl status nginx
```

**Expected output:** `active (running)`

---

## Step 9: Test Installation

### 9.1 Create Test PHP File

```bash
# Create a test file
sudo nano /var/www/caseace/test.php
```

Add this content:

```php
<?php
echo "✅ PHP is working!<br>";
echo "PHP Version: " . phpversion() . "<br>";
echo "Composer: ";
if (file_exists('/var/www/caseace/vendor/autoload.php')) {
    echo "✅ Installed<br>";
} else {
    echo "❌ Not installed<br>";
}
echo "npm: ";
if (file_exists('/var/www/caseace/node_modules/axios')) {
    echo "✅ Installed<br>";
} else {
    echo "❌ Not installed<br>";
}
echo ".env file: ";
if (file_exists('/var/www/caseace/.env')) {
    echo "✅ Found<br>";
} else {
    echo "❌ Not found<br>";
}
?>
```

**To save:** Ctrl+O → Enter → Ctrl+X

### 9.2 Test in Browser

Open your browser and visit:

```
http://your_domain.com/test.php
```

or if testing locally:

```
http://your_server_ip/test.php
```

**Expected output:**
```
✅ PHP is working!
PHP Version: 8.0.x
Composer: ✅ Installed
npm: ✅ Installed
.env file: ✅ Found
```

### 9.3 Test Main Application

Visit the main application:

```
http://your_domain.com/
```

or

```
http://your_server_ip/
```

### 9.4 Check Logs for Errors

```bash
# Check Nginx error log
sudo tail -f /var/log/nginx/caseace_error.log

# Check PHP-FPM error log
sudo tail -f /var/log/php-fpm/www-error.log

# Check system log
sudo journalctl -u nginx -f
```

### 9.5 Clean Up Test File

```bash
# Remove test file after verification
sudo rm /var/www/caseace/test.php
```

---

## Step 10: Post-Installation Configuration

### 10.1 Configure Backend API Connection

Edit your `.env` file to point to your backend API:

```bash
# Edit .env
sudo nano /var/www/caseace/.env
```

Update:
```env
API_BASE_URL=http://your_backend_domain.com/api
BACKEND_URL=http://your_backend_domain.com/api
```

### 10.2 Setup SSL/TLS (HTTPS) - Recommended

```bash
# Install Certbot
sudo dnf install -y certbot python3-certbot-nginx

# Generate SSL certificate
sudo certbot certonly --nginx -d your_domain.com -d www.your_domain.com

# Update Nginx config with SSL
sudo nano /etc/nginx/conf.d/caseace.conf
```

Add these lines at the end (before closing brace):

```nginx
    # SSL/TLS configuration
    listen 443 ssl;
    ssl_certificate /etc/letsencrypt/live/your_domain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/your_domain.com/privkey.pem;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;

# Redirect HTTP to HTTPS
server {
    listen 80;
    server_name your_domain.com www.your_domain.com;
    return 301 https://$server_name$request_uri;
}
```

Reload Nginx:

```bash
sudo systemctl reload nginx
```

### 10.3 Setup Automatic SSL Renewal

```bash
# Test auto-renewal
sudo certbot renew --dry-run

# Enable automatic renewal cron
sudo systemctl enable certbot.timer
sudo systemctl start certbot.timer
```

---

## Troubleshooting

### Issue 1: "Permission denied" when running commands

**Solution:**
```bash
# Make sure you're using sudo
sudo chown -R nginx:nginx /var/www/caseace
sudo chmod -R 755 /var/www/caseace
```

### Issue 2: Nginx shows "502 Bad Gateway"

**Solution:**
```bash
# Check PHP-FPM status
sudo systemctl status php-fpm

# Check PHP-FPM error log
sudo tail -20 /var/log/php-fpm/www-error.log

# Restart PHP-FPM
sudo systemctl restart php-fpm
sudo systemctl restart nginx
```

### Issue 3: "Connection refused" for API requests

**Solution:**
```bash
# Verify backend API is running
curl http://your_backend_domain.com/api/health

# Check .env API_BASE_URL setting
sudo grep API_BASE_URL /var/www/caseace/.env

# Check browser console for CORS errors (F12)
```

### Issue 4: File upload errors

**Solution:**
```bash
# Verify uploads directory permissions
ls -la /var/www/caseace/uploads

# Fix permissions
sudo chown nginx:nginx /var/www/caseace/uploads
sudo chmod 755 /var/www/caseace/uploads

# Check max upload size in PHP
php -i | grep post_max_size
php -i | grep upload_max_filesize

# Edit PHP config if needed
sudo nano /etc/php.ini
# Set: post_max_size = 10M
# Set: upload_max_filesize = 10M
# Restart PHP-FPM: sudo systemctl restart php-fpm
```

### Issue 5: ".env file not found" error at runtime

**Solution:**
```bash
# Verify .env file exists
sudo ls -la /var/www/caseace/.env

# Check if it's readable by nginx user
sudo -u nginx cat /var/www/caseace/.env

# If missing, recreate it
cd /var/www/caseace
sudo nano .env
# Add configuration (see Step 7.2)
sudo chown nginx:nginx .env
sudo chmod 600 .env
```

### Issue 6: Composer/npm install fails

**Solution:**
```bash
# Clear Composer cache
composer clear-cache

# Clear npm cache
npm cache clean --force

# Try installing again
cd /var/www/caseace
sudo -u nginx composer install
sudo -u nginx npm install

# Check PHP extensions are installed
php -m | grep curl
php -m | grep json
```

### Issue 7: Page shows "Access Denied"

**Solution:**
```bash
# Fix SELinux context
sudo chcon -R -t httpd_sys_rw_content_t /var/www/caseace

# Or temporarily disable SELinux
sudo setenforce 0

# Check file permissions
sudo ls -la /var/www/caseace/index.php
```

---

## Useful Commands for Management

### Restart Services

```bash
# Restart Nginx
sudo systemctl restart nginx

# Restart PHP-FPM
sudo systemctl restart php-fpm

# Restart both
sudo systemctl restart nginx php-fpm
```

### View Logs

```bash
# Nginx access logs
sudo tail -100 /var/log/nginx/caseace_access.log

# Nginx error logs
sudo tail -100 /var/log/nginx/caseace_error.log

# PHP error logs
sudo tail -100 /var/log/php-fpm/www-error.log

# System logs
sudo journalctl -u nginx -n 100
sudo journalctl -u php-fpm -n 100
```

### Update Application

```bash
# Pull latest changes
cd /var/www/caseace
sudo git pull origin main

# Update dependencies
sudo -u nginx composer update --no-dev
sudo -u nginx npm update

# Restart services
sudo systemctl restart nginx php-fpm
```

### Monitor Performance

```bash
# Check disk usage
df -h

# Check memory usage
free -h

# Check CPU usage
top

# Check nginx worker processes
ps aux | grep nginx

# Check PHP-FPM processes
ps aux | grep php-fpm
```

---

## Quick Reference - All Commands in Order

```bash
# 1. Update system
sudo dnf update -y && sudo dnf upgrade -y
sudo dnf install -y git wget curl vim nano htop

# 2. Install PHP & Nginx
sudo dnf config-manager --set-enabled powertools
sudo dnf install -y php php-cli php-fpm php-common php-curl php-json
sudo dnf install -y nginx

# 3. Start services
sudo systemctl start nginx php-fpm
sudo systemctl enable nginx php-fpm

# 4. Install Node.js
sudo dnf module enable nodejs:18
sudo dnf install -y nodejs

# 5. Install Composer
curl -sS https://getcomposer.org/installer -o composer-setup.php
sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
rm composer-setup.php

# 6. Clone repository
sudo mkdir -p /var/www/caseace
cd /var/www/caseace
sudo git clone https://github.com/HendersonW2/CaseAce-law-firm-management-system-frontend.git .

# 7. Fix permissions
sudo chown -R nginx:nginx /var/www/caseace
sudo chmod -R 755 /var/www/caseace

# 8. Install dependencies
cd /var/www/caseace
sudo -u nginx composer install --no-dev
sudo -u nginx npm install --production

# 9. Create .env
sudo nano /var/www/caseace/.env
# (Add configuration from Step 7.2)
sudo chown nginx:nginx .env
sudo chmod 600 .env

# 10. Create Nginx config
sudo nano /etc/nginx/conf.d/caseace.conf
# (Add configuration from Step 8.2)

# 11. Test and reload
sudo nginx -t
sudo systemctl reload nginx

# 12. Verify
curl http://localhost/test.php
```

---

## Server Stats Summary

📊 **Your Server Configuration:**
- ✅ AlmaLinux 9
- ✅ 2 CPUs
- ✅ 4GB RAM (sufficient for this app)
- ✅ 110GB Storage (plenty - app uses ~500MB)

⚡ **Performance Optimization:**
- PHP 8.0+ (fast)
- Nginx (lightweight)
- Production mode enabled (.env: APP_DEBUG=false)
- Auto-optimized Composer

---

## Support & Documentation

- **GitHub Repository:** https://github.com/HendersonW2/CaseAce-law-firm-management-system-frontend
- **Original Repository:** https://github.com/derekgan08/CaseAce-law-firm-management-system-frontend
- **PHP Docs:** https://www.php.net/docs.php
- **Nginx Docs:** https://nginx.org/en/docs/
- **AlmaLinux Docs:** https://wiki.almalinux.org/

---

**Installation completed successfully! 🎉**

**Next Steps:**
1. Access application at `http://your_domain.com`
2. Configure backend API connection
3. Setup SSL certificate (recommended)
4. Test all features

---

**Last Updated:** January 2026 | Version 1.0 | AlmaLinux 9 Specific
