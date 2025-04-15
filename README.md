# 💻 phpMyAdmin Setup Guide (PHP 8.1 + Nginx)

A simple guide to install and configure phpMyAdmin using PHP 8.1 and Nginx on Ubuntu.

---

## ✅ 0. Install Required PHP Extensions

```bash
sudo apt install php8.1-mbstring php8.1-zip php8.1-gd php8.1-json php8.1-curl php8.1-bcmath
```

> Replace `8.1` with your PHP version if needed.

If you're using **PHP-FPM** with **Nginx**, also install:

```bash
sudo apt install php8.1-fpm php8.1-mysql
```

---

## 📅 1. Download phpMyAdmin

- Download the latest version from the [official site](https://www.phpmyadmin.net)
- Or use the provided folder: `phpMyAdmin-5.2.1-all-languages`

---

## 📂 2. Set Up Directory

```bash
sudo mkdir /var/www/phpmyadmin
sudo cp -r phpMyAdmin-*/* /var/www/phpmyadmin/
```

---

## ⚙️ 3. Configure Nginx

Edit your Nginx site config at `/etc/nginx/sites-available/default` or create a new one:

```nginx
server {
    listen 9999;
    server_name localhost;

    root /var/www/phpmyadmin;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }

    location /phpmyadmin {
        root /usr/share/;
        index index.php index.html index.htm;
    }

    location ~ /phpmyadmin/(.+\.php)$ {
        root /usr/share/;
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~* ^/phpmyadmin/(.+\.(jpg|jpeg|gif|css|png|js|ico|html|xml|txt))$ {
        root /usr/share/;
    }
}
```

Save and close the file.

---

## 🔐 4. Configure phpMyAdmin

Generate a secret passphrase:

```bash
openssl rand -base64 32
```

Copy the output and save it. Now run:

```bash
sudo cp /var/www/phpmyadmin/config.sample.inc.php /var/www/phpmyadmin/config.inc.php
sudo nano /var/www/phpmyadmin/config.inc.php
```

Find the following line:

```php
$cfg['blowfish_secret'] = '';
```

Replace it with:

```php
$cfg['blowfish_secret'] = 'your_generated_passphrase';
```

---

## 🔁 5. Restart Services

```bash
sudo systemctl restart php8.1-fpm
sudo systemctl reload nginx
```

---

## ✅ Done!

Access phpMyAdmin at:

```
http://localhost:9999
```

---

### 📋 Notes

- Make sure your firewall allows port `9999` or change it to `80` if needed.
- Replace `php8.1` with your actual PHP version if different.

---

**Happy Hosting!** 🚀

