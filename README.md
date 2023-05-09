# Vagrant-IAAC-WordPress-Site-Deployment
About Infrastructure As A Code Deployment of a WordPress Site using Vagrant onto a Ubuntu 20.04 VM. The provision script below deploys a WordPress website onto an Ubuntu VM programmatically.

## Provision Code
```
config.vm.provision "shell", inline: <<-SHELL
    sudo apt-get update
    sudo apt-get install apache2 \
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
      php-zip -y
    
    sudo mkdir -p /srv/www
    sudo chown www-data: /srv/www
    curl https://wordpress.org/latest.tar.gz | sudo -u www-data tar zx -C /srv/www
    cp /vagrant/wordpress.conf /etc/apache2/sites-available/wordpress.conf

    sudo a2ensite wordpress
    sudo a2enmod rewrite
    sudo a2dissite 000-default
    sudo service apache2 reload

    mysql -u root -e 'CREATE DATABASE wordpress;'
    mysql -u root -e 'CREATE USER wordpress@localhost IDENTIFIED BY "12345";'
    mysql -u root -e 'GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER ON wordpress.* TO wordpress@localhost;'
    mysql -u root -e 'FLUSH PRIVILEGES;'

    sudo -u www-data cp /srv/www/wordpress/wp-config-sample.php /srv/www/wordpress/wp-config.php
    sudo -u www-data sed -i 's/database_name_here/wordpress/' /srv/www/wordpress/wp-config.php
    sudo -u www-data sed -i 's/username_here/wordpress/' /srv/www/wordpress/wp-config.php
    sudo -u www-data sed -i 's/password_here/12345/' /srv/www/wordpress/wp-config.php
SHELL
```
