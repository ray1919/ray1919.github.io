# Upgrade Ubuntu PHP5 to PHP7

### Upgrade Ubuntu to 16.04 will have PHP7 by default, and the original website might not working anymore, here is my solution.
  1. Change DocumentRoot
  ``` sh
  cd /etc/apache2/
  sudo vi sites-available/000-default.conf
  ```
  2. install php7 mods packages
  ``` sh
  sudo apt-get install php7.0 libapache2-mod-php7.0 php7.0-mysql php7.0-gd \
  php7.0-mcrypt php7.0-sqlite3 php7.0-pgsql php7.0-soap php7.0-bz2 php7.0-curl \
  php7.0-json php7.0-dev php7.0-opcache php7.0-zip php7.0-xsl php7.0-xml \
  php7.0-intl php7.0-mbstring php-memcached php-imagick
  ```
  3. Enable .htaccess
  ``` sh
  sudo vi apache2.conf
  replace "AllowOverride None" with "AllowOverride All"
  ```
  4. turn on php engine in userdir
  ``` sh
  sudo vi mods-available/php7.0.conf
  change "php_admin_flag engine Off" to On
  ```
  5. change Options in userdir.conf
  ``` sh
  sudo vi mods-enabled/userdir.conf
  add "FollowSymLinks Includes" in Options
  ```
  6. Install shiny server
  ``` sh
  sudo apt-get install r-base gdebi-core
  sudo gdebi rstudio-server-1.0.136-amd64.deb
  sudo gdebi shiny-server-1.5.3.838-amd64.deb
  ```
  7. restart apache2 service
  ``` sh
  sudo service apache2 restart
  ```
### It finally works.
