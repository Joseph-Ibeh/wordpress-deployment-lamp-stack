# Setting up Ubuntu VM with LAMP Stack and Deploying WordPress

## Overview
WordPress is the most popular open-source blogging system and CMS on the web. It is based on PHP and MySQL and can be extended with thousands of free plugins and themes.

This documentation describes the steps I used to install WordPress on an Apache2 server. It also covers how I configured a database for WordPress on my local machine.


## Basics

1. Created a directory `wordpress` in my Git Bash CLI.
`sudo mkdir -p /srv/www`

2. Initialized a Vagrant box with the command:
    ` vagrant init ubuntu/focal64`

3. Edited the Vagrantfile with:
vim Vagrantfile

-Uncommented the # symbol for config.vm.network "private_network" and set it to IP 192.168.56.26.
-Uncommented the # symbol for config.vm.network "public_network".
-Uncommented the # symbol for config.vm.provider "virtualbox".
-Set vb.memory to "1600".
-Saved and exited with Esc :wq.

4. Started and connected to the VM:
`vagrant up`
`vagrant ssh`



5. Switched to the root user: 
 `sudo -i`

6. Changed the hostname:

  `vim /etc/hostname`

  Typed wordpress, then saved and exited.

7. Logged out and back in to apply changes:

  `exit`
  `vagrant ssh`
  `sudo -i`


## Installed and Configured WordPress

Step 1: Installed  Dependencies
First, i updated the package list:

`sudo apt update`

Then, i installed PHP, Apache, MySQL, and other necessary packages:

`sudo apt install apache2 \
                 ghostscript \
                 libapache2-mod-php \
                 mysql-server \
                 php \
                 php-bcmath \
                 php-curl \
                 php-imagick \
                 php-intl \
                 php-json \
                 php-mbstring \
                 php-mysql \
                 php-xml \
                 php-zip -y`

To check the installation, i used:
 ` ls -ld /srv/www/`
  `ls -l /srv/www/wordpress/`



Step 2: Installed  WordPress

  `sudo mkdir -p /srv/www`
  `sudo chown www-data: /srv/www`
  `curl https://wordpress.org/latest.tar.gz | sudo -u www-data tar zx -C /srv/www`



Step 3: Configured Apache for WordPress
-I Opened the configuration file:
  
`vim /etc/apache2/sites-available/wordpress.conf`

-Added the following configuration:

`<VirtualHost *:80>
    DocumentRoot /srv/www/wordpress
    <Directory /srv/www/wordpress>
        Options FollowSymLinks
        AllowOverride Limit Options FileInfo
        DirectoryIndex index.php
        Require all granted
    </Directory>
    <Directory /srv/www/wordpress/wp-content>
        Options FollowSymLinks
        Require all granted
    </Directory>
</VirtualHost>`

Saved and exited with `Esc` `:wq`

- Enabled the Site and URL Rewriting by using this command one after the other 

  `sudo a2ensite wordpress`
  `sudo a2enmod rewrite`
  `sudo a2dissite 000-default`
  `sudo service apache2 reload`



Step 4: Configured the Database
Connected to MySQL and created the database and user:
  
  `sudo mysql -u root`


-Ran the following commands one after the other "admin123 is the password i chose".

`CREATE DATABASE wordpress;`
`CREATE USER wordpress@localhost IDENTIFIED BY 'admin123';`
`GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER ON wordpress.* TO wordpress@localhost;`
`FLUSH PRIVILEGES;`
`quit;`



Step 5: Configured WordPress to Connect to the Database.
I used this command  one after the other. I used admin123 as seen in this lines of code as my password.

  `sudo -u www-data cp /srv/www/wordpress/wp-config-sample.php /srv/www/wordpress/wp-config.php`
  `sudo -u www-data sed -i 's/database_name_here/wordpress/' /srv/www/wordpress/wp-config.php`
  `sudo -u www-data sed -i 's/username_here/wordpress/' /srv/www/wordpress/wp-config.php`
  `sudo -u www-data sed -i 's/password_here/admin123/' /srv/www/wordpress/wp-config.php`

- Added Security Keys
Opened the configuration file:

  `sudo -u www-data vim /srv/www/wordpress/wp-config.php`

I Found and deleted the following lines:

 ` define( 'AUTH_KEY',         'put your unique phrase here' );
  define( 'SECURE_AUTH_KEY',  'put your unique phrase here' );
  define( 'LOGGED_IN_KEY',    'put your unique phrase here' );
  define( 'NONCE_KEY',        'put your unique phrase here' );
  define( 'AUTH_SALT',        'put your unique phrase here' );
  define( 'SECURE_AUTH_SALT', 'put your unique phrase here' );
  define( 'LOGGED_IN_SALT',   'put your unique phrase here' );
  define( 'NONCE_SALT',       'put your unique phrase here' ); `

  I Replaced them with content from https://api.wordpress.org/secret-key/1.1/salt/. Save and close (ctrl+x followed by y then enter).



Step 6: Configured WordPress from the Browser
Ran the following to get my ip address:

  `ip addr show`

- I Copied  the IP, pasted it in my browser, and completed the WordPress setup:
Entered my site title, username, password, and email.
Clicked “Install WordPress” and logged in to start.





## Skills and Lessons Acquired

- Set Up a Virtual Machine (VM): Initialized and configured a virtual machine with Ubuntu using Vagrant, enhancing understanding of virtual machine provisioning and customization.
  
- Install and Configure a LAMP Stack: Installed essential components of the LAMP stack (Apache, MySQL, and PHP) for running WordPress, managing package updates and service configurations.
  
- Deploy WordPress on Apache: Created a directory structure for WordPress, set file permissions, and downloaded the latest WordPress release, gaining insight into web application deployment and security practices.
  
- Configure Apache Virtual Hosts: Configured a virtual host to serve the WordPress site, enabling URL rewriting and adjusting permissions, learning about hosting multiple sites on a single server.
  
- Create and Secure a MySQL Database: Established a dedicated database and user for WordPress, set permissions, and configured the database connection, reinforcing database management skills.
  
- Secure the WordPress Configuration: Generated unique authentication keys and salts using the WordPress API, enhancing the security of the `wp-config.php` file.
  
- Complete the WordPress Setup in a Browser: Completed the WordPress installation through the browser, providing understanding of the initial setup workflow.



















