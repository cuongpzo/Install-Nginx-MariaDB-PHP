# Install Nginx - MariaDB - PHP
## Pre-Install 
```
sudo apt install -y openssh-client openssh-server net-tools curl python3 unzip zip default-jre tasksel perl nano

```

## 1. Firewall Setup
```
sudo ufw allow ssh
sudo ufw allow http
sudo ufw enable

```
## 2. Update Ubuntu
```
sudo apt update
sudo apt -y upgradde

```
## 3. Install Nginx
```
sudo apt install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx

```

## 4. Install PHP
```
sudo apt-get install software-properties-common -y
sudo add-apt-repository ppa:ondrej/php
```
```
sudo apt install -y php-common php-mysql php-xml php-xmlrpc php-curl php-gd php-imagick php-cli php-dev php-imap php-mbstring php-opcache php-soap php-zip php-gmp php-intl php-fpm  php-dom php-json php-fileinfo  -y

```
## 5. Config Nginx
```
sudo rm -R /etc/nginx/sites-available/default
sudo rm -R /etc/nginx/sites-enabled/default
```
Create & Edit file Configuration 
```
sudo nano /etc/nginx/sites-available/php_wordpress
```
```
	server {
		listen 80;
		listen [::]:80;
		root /var/www/php_wordpress;

		index  index.php index.html index.htm a.a;
		server_name  serverphp.vltkk ;

		client_max_body_size 2G;
		autoindex off;
		location / {
			try_files $uri $uri/ /index.php?$args;
		}

		location ~ \.php$ {
			 include snippets/fastcgi-php.conf;
			 fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
			 fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
			 include fastcgi_params;
		}
	}
```
"fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;" 

Configuring this file depends on the PHP version you have installed. If it's 7.4, set it up this way; otherwise, you can customize it with the correct path.
```
ln -s /etc/nginx/sites-available/php_wordpress /etc/nginx/sites-enabled/
```












