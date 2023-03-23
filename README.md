# Wordpress Installation

## Requirements
Make sure you have a working VPS with a user that is able to sudo

1. Update your server's packages:
```bash
sudo apt update && sudo apt upgrade
```
2. Install Apache2 and enable php to be executed:
```bash
sudo apt install apache2
sudo a2dismod mpm_event # Needs to be disabled for php module to work
sudo a2enmod php8.1 # make apache2 execute php files before responding to user request
sudo systemctl restart apache2 
```

3. Verify that Apache2 is running:
```bash
sudo systemctl status apache2
```

4. Install MariaDB:
```bash
sudo apt install mariadb-server
```

5. Run the MariaDB secure installation script and follow the prompts to set a root password and secure the installation:
```bash
sudo mysql_secure_installation
```

6. Log into MariaDB with the root password you just set:
```bash
sudo mysql -u root -p
```

7. Create a new database and user for WordPress:
```sql
CREATE DATABASE wordpress;
CREATE USER 'wordpressuser'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpressuser'@'localhost';
FLUSH PRIVILEGES;
# Note: Replace 'password' with a secure password of your choice.
EXIT;
```

8. Download and extract the latest version of WordPress to the Apache2 web root directory:
```bash
cd /var/www/wordpress
sudo wget https://wordpress.org/latest.tar.gz
sudo tar -xzvf latest.tar.gz
sudo rm latest.tar.gz
```

9. Set the appropriate permissions:
```bash
sudo chown -R www-data:www-data /var/www/wordpress/
sudo chmod -R 755 /var/www/wordpress/
```

10. Create an Apache2 virtual host configuration file for your domain. Replace wordpress with your domain name or just comment it out if you dont have a domain:

```bash
sudo nano /etc/apache2/sites-available/wordpress.conf
```

11. Add the following configuration to the file:
```c
<VirtualHost *:80>
    DocumentRoot /var/www/wordpress/
    # domain
    <Directory /var/www/wordpress/>
        Options FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/example.com_error.log
    CustomLog ${APACHE_LOG_DIR}/example.com_access.log combined
</VirtualHost>
```

12. Enable the virtual host:
```bash
sudo a2ensite wordpress.conf
```

13. Disable the default Apache2 virtual host:
```bash
sudo a2dissite 000-default.conf
```

14. Restart Apache2:
```bash
sudo systemctl restart apache2
```

Open your web browser and navigate to your domain name or IP adress. You should see the WordPress installation page. Follow the prompts to complete the installation.
That's it! You now have WordPress installed with MariaDB and Apache2 on your Ubuntu VPS.
