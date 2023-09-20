# Install Nginx - MariaDB - PHP (+Install Wordpress)
## Pre-Install 
```
sudo apt install -y openssh-client openssh-server net-tools curl python3 unzip zip default-jre tasksel perl nano

```

## 1. Firewall Setup
```
sudo ufw allow ssh

```
```
sudo ufw allow http
sudo ufw enable

```
## 2. Update Ubuntu
```
sudo apt update

```
```
sudo apt -y upgrade

```
## 3. Install Nginx
```
sudo apt install nginx -y

```
```
sudo systemctl start nginx
sudo systemctl enable nginx

```

## 4. Install PHP
```
sudo apt-get install software-properties-common -y

```
```
sudo add-apt-repository ppa:ondrej/php

```
```
sudo apt install -y php-common php-mysql php-xml php-xmlrpc php-curl php-gd php-imagick php-cli php-dev php-imap php-mbstring php-opcache php-soap php-zip php-gmp php-intl php-fpm  php-dom php-json php-fileinfo  -y

```

Config file "php.ini", FRM standard (php v7.4).
When installing, you can check if you've installed the correct PHP version, and if necessary, adjust it accordingly.
```
sed -i '/file_uploads =/c\file_uploads = On ;' /etc/php/7.4/fpm/php.ini
sed -i '/max_input_vars =/c\max_input_vars = 4000 ;' /etc/php/7.4/fpm/php.ini
sed -i '/allow_url_fopen =/c\allow_url_fopen = On ;' /etc/php/7.4/fpm/php.ini
sed -i '/short_open_tag =/c\short_open_tag = On ;' /etc/php/7.4/fpm/php.ini
sed -i '/memory_limit =/c\memory_limit = 512M ;' /etc/php/7.4/fpm/php.ini
sed -i '/post_max_size =/c\post_max_size = 2000M ;' /etc/php/7.4/fpm/php.ini
sed -i '/cgi.fix_pathinfo =/c\cgi.fix_pathinfo = 0 ;' /etc/php/7.4/fpm/php.ini
sed -i '/upload_max_filesize =/c\upload_max_filesize = 2000M ;' /etc/php/7.4/fpm/php.ini
sed -i '/max_execution_time =/c\max_execution_time = 36000000 ;' /etc/php/7.4/fpm/php.ini
sed -i '/max_input_time =/c\max_input_time = 600000000 ;' /etc/php/7.4/fpm/php.ini
sed -i '/default_charset =/c\default_charset = "UTF-8" ;' /etc/php/7.4/fpm/php.ini
sed -i '/max_file_uploads =/c\max_file_uploads = 20000 ;' /etc/php/7.4/fpm/php.ini

```

## 5. Config Nginx
### Create Folder Sourcecode web
```
mkdir  /var/www/php_wordpress

```
### And delete the existing default configuration file.
```
sudo rm -R /etc/nginx/sites-available/default
sudo rm -R /etc/nginx/sites-enabled/default

```
### Create & Edit file Configuration 
```
sudo nano /etc/nginx/sites-available/php_wordpress

```
```
	server {
		listen 80;
		listen [::]:80;
		root /var/www/php_wordpress;

		index  index.php index.html index.htm a.a;
		server_name  serverphp ;

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
### "fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;" 

Configuring this file depends on the PHP version you have installed. If it's 7.4, set it up this way; otherwise, you can customize it with the correct path.
```
ln -s /etc/nginx/sites-available/php_wordpress /etc/nginx/sites-enabled/

```

## 6. Install MariaDB
```
sudo apt install mariadb-server mariadb-client -y

```
```
sudo systemctl stop mysql.service
sudo systemctl start mysql.service
sudo systemctl enable mysql.service

```
```
mysql_secure_installation

```
You can set any password of your choice to configure Mariadb. etc: "passwordmdb" 
And, create Account/Passwd for Database. Full access rights.
```
sudo mysql -u root -p passwordmdb

```
```
	GRANT ALL PRIVILEGES ON *.* to 'account1'@'%' identified by 'passwordmdb1';
	FLUSH PRIVILEGES;
	GRANT ALL PRIVILEGES ON *.* to 'account2'@'%' identified by 'passwordmdb2';
	FLUSH PRIVILEGES;

	CREATE DATABASE php_wordpress;
	CREATE DATABASE php_mybb;
	EXIT;

```

## 7. Test
### Create file "info.php"
```
echo "
<?php
phpinfo();
?>
">> /var/www/php_wordpress/phpinfo.php

```
```
chmod +x -R /var/www/php_wordpress/phpinfo.php

```
```
systemctl restart nginx.service

```


## 8. Install Wordpress.

```
cd  /var/www/php_wordpress

```
```
wget https://wordpress.org/latest.tar.gz

```
```
tar -xvzf latest.tar.gz

```
```
mv /var/www/php_wordpress/wordpress/* /var/www/php_wordpress/

```
```
chown www-data:www-data 0777 *

```
```
systemctl restart nginx.service

```

### Adding the following code to the end of the wp-config.php file via FTP will prevent WordPress from asking for an FTP username and password when installing plugins or themes:

```php
define('FS_METHOD', 'direct');
```
or
```
if(is_admin()) {
add_filter('filesystem_method', create_function('$a', 'return "direct";' ));
define('FS_CHMOD_DIR',0751);
}

```

This code configures WordPress to use the direct file system method, which allows WordPress to write to the filesystem directly without asking for FTP credentials. However, please be cautious when making changes to your wp-config.php file, and make sure you have a backup in case anything goes wrong during the editing process.


# END




