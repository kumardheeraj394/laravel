
# WordPress + Laravel + MySQL on Nginx (Ubuntu EC2)

This project sets up **WordPress**, **Laravel**, and **MySQL** on a **single Nginx server** hosted on an **Ubuntu EC2 instance**. Useful for running PHP-based CMS and applications side-by-side.

---

## 🚀 Technologies Used

- Ubuntu 20.04 / 22.04 (EC2)
- Nginx
- PHP (FPM)
- MySQL Server
- WordPress
- Laravel
- Composer
- Git

---

## 🛠️ Setup Instructions

### 1. Launch EC2

- OS: Ubuntu 20.04 or 22.04
- Open ports in Security Group: `22`, `80`, `443`

SSH into the instance:

```bash
ssh -i your-key.pem ubuntu@your-ec2-ip
```

---

### 2. Install Required Packages

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install nginx mysql-server php php-fpm php-mysql php-mbstring php-xml php-curl php-zip unzip git curl composer -y
```

---

### 3. Setup MySQL

```bash
sudo mysql_secure_installation
```

```bash
sudo mysql -u root -p
```

Then login and create databases and users:

```sql
CREATE DATABASE wordpress_db;
CREATE USER 'wp_user'@'localhost' IDENTIFIED BY 'StrongPassword1!';
GRANT ALL PRIVILEGES ON wordpress_db.* TO 'wp_user'@'localhost';

CREATE DATABASE laravel_db;
CREATE USER 'laravel_user'@'localhost' IDENTIFIED BY 'StrongPassword2!';
GRANT ALL PRIVILEGES ON laravel_db.* TO 'laravel_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

### 4. Install WordPress

```bash
cd /var/www/
sudo curl -O https://wordpress.org/latest.tar.gz
sudo tar -xzvf latest.tar.gz
sudp cd wordpress
sudo cp wp-config-sample.php wp-config.php
```

Edit `wp-config.php`:

```php
sudo vi /var/www/wordpress/wp-config.php
define('DB_NAME', 'wordpress_db');
define('DB_USER', 'wp_user');
define('DB_PASSWORD', 'StrongPassword1!');
define('DB_HOST', 'localhost');
```

Set permissions:

```bash
sudo chown -R www-data:www-data /var/www/wordpress
sudo chmod -R 755 /var/www/wordpress
```

---

### 5. Install Laravel

```bash
cd /var/www/
sudo git clone https://github.com/laravel/laravel.git laravel
sudo cd laravel
sudo composer install
sudo chown -R www-data:www-data /var/www/laravel
sudo cp .env.example .env
sudo php artisan key:generate
```

sudo touch /var/www/laravel/database/database.sqlite

Edit `.env` and set DB config:

```env
su vi .env
DB_CONNECTION=sqlite
DB_DATABASE=/var/www/laravel/database/database.sqlite
#DB_USERNAME=laravel_user
#DB_PASSWORD=StrongPassword2!
```

Permissions:

```bash
sudo chown -R www-data:www-data /var/www/laravel
sudo chmod -R 755 /var/www/laravel-app/storage
```

---

### 6. Configure Nginx

Edit default config:

```bash
sudo vi /etc/nginx/sites-available/default
```

Replace content:

```nginx
server {
    listen 80;
    server_name _;

    root /var/www;
    index index.php index.html index.htm;

    location /wordpress {
        alias /var/www/wordpress-site;
        index index.php;
        try_files $uri $uri/ /index.php?$args;

        location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/run/php/php-fpm.sock;
        }
    }

    location /laravel {
        alias /var/www/laravel-app/public;
        index index.php;
        try_files $uri $uri/ /index.php?$query_string;

        location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/run/php/php-fpm.sock;
        }
    }

    location ~ /\.ht {
        deny all;
    }
}
```

Restart Nginx:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

---

## ✅ Access in Browser

- Laravel: `http://your-ec2-ip/laravel`
- WordPress: `http://your-ec2-ip/wordpress`

---

## 🔐 Optional

- Use Certbot to enable SSL:
  ```bash
  sudo apt install certbot python3-certbot-nginx -y
  sudo certbot --nginx
  ```
- Use UFW to firewall unused ports.

---

## 📂 Directory Structure

```
/var/www/
├── laravel-app/        → Laravel project
└── wordpress-site/     → WordPress site
```

---

## 🧑‍💻 Author

Created by [Your Name] — tested on Ubuntu EC2

---

sudo ufw status

sudo ufw allow 22
sudo ufw allow 80
sudo ufw allow 443
sudo ufw allow 8080
sudo ufw allow 8081

sudo ufw enable
or

sudo ufw disable
---
