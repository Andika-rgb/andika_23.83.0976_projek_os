# andika_23.83.0976_projek_os
andika hendrawan_23.83.0976_23TK01
# tanggal menambahkan layanan server:
1. apache2, 1 desember 2024
2. mysql, 14 desember 2024
3. wordpress 14 desember 2024

# Judul: Manajemen Server Terpadu dengan Nginx, PostgreSQL, Flask, Grafana, dan Keycloak

# Rombakan
# 1.APACHE2
# Instal Apache2
    sudo apt update
    sudo apt install apache2
# Aktifkan Apache2:
    sudo systemctl start apache2
    sudo systemctl enable apache2
# Menyalin github
     sudo apt install git
     sudo git clone https://github.com/Andika-rgb/AndikaTravel.github.io.gi
# membuat direktori 
    sudo mkdir -p /var/www/server1
# konfigurasi apache2
    sudo nano /etc/apache2/sites-available/server1.conf

    <VirtualHost *:80>
        ServerAdmin admin@192.168.100.58
        DocumentRoot /var/www/server1/AndikaTravel.github.io
        ServerName 192.168.100.58
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>
# Aktifkan kembali konfigurasi virtual host:
    sudo a2ensite server1.conf
    sudo systemctl reload apache2
# Buka firewall untuk HTTP
    sudo ufw allow 'Apache'
    sudo ufw status
# Pastikan direktori memiliki hak akses yang benar:
    sudo chown -R www-data:www-data /var/www/server1/AndikaTravel.github.io
    sudo chmod -R 755 /var/www/server1/AndikaTravel.github.io

# 2. Mysql

# Instal MySQL:
    sudo apt install mysql-server
# Instal PHP dan modul yang diperlukan:
    sudo apt install php libapache2-mod-php php-mysql php-xml php-mbstring
# Masuk ke MySQL, dan Buat database dan user untuk WordPress::
    sudo mysql

    CREATE DATABASE wordpress;
    CREATE USER 'dika'@'192.168.100.58' IDENTIFIED BY 'terlalu12';
    GRANT ALL PRIVILEGES ON wordpress.* TO 'dika'@'192.168.100.58';
    FLUSH PRIVILEGES;
    EXIT;
    
# 3. Wordpress

# Unduh dan Ekstrak WordPress:
    cd /var/www/
    sudo curl -O https://wordpress.org/latest.tar.gz
    sudo tar xzvf latest.tar.gz
    sudo mv wordpress /var/www/server1/AndikaTravel.github.io
    sudo cp wp-config-sample.php wp-config.php
# Set Hak Akses:
    sudo chown -R www-data:www-data /var/www/server1/AndikaTravel.github.io
    sudo chmod -R 755 /var/www/server1/AndikaTravel.github.io
    
# Konfigurasi Apache untuk WordPress:
    sudo nano /etc/apache2/sites-available/your-website.conf

    <VirtualHost *:80>
        ServerAdmin admin@192.168.100.58
        DocumentRoot /var/www/server1/AndikaTravel.github.io
        ServerName 192.168.100.58
        <Directory /var/www/server1/AndikaTravel.github.io>
            AllowOverride All
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>
# Aktifkan situs dan modul rewrite:
    sudo a2ensite server1.conf
    sudo a2enmod rewrite
    sudo systemctl reload apache2
    
    penjelasan: saya mengkonfigurasi WordPress pada server yang saya host sendiri menggunakan Apache web server, yang memungkinkan saya memiliki kontrol penuh atas lingkungan hosting saya. Ini juga memastikan bahwa situs web saya dapat menangani lebih banyak lalu lintas dan saya dapat menyesuaikan server sesuai kebutuhan spesifik saya.

# Edit wp-config.php, samakan seperti tadi membuat databse di mySQL:
    sudo nano /var/www/server1/AndikaTravel.github.io/wordpress/wp-config.php

    /** The name of the database for WordPress */
    define('DB_NAME', 'wordpress');
    
    /** MySQL database username */
    define('DB_USER', 'dika');
    
    /** MySQL database password */
    define('DB_PASSWORD', 'terlalu12');
    
    /** MySQL hostname */
    define('DB_HOST', '192.168.100.58');
    
    /** Database Charset to use in creating database tables. */
    define('DB_CHARSET', 'utf8mb4');
    
    /** The Database Collate type. Don't change this if in doubt. */
    define('DB_COLLATE', '');

# Pastikan Basis Data MySQL Berjalan:
    sudo systemctl start mysql
    sudo systemctl restart mysql
    sudo systemctl status mysql
Setelah melakukan langkah-langkah ini, coba lagi untuk mengakses URL instalasi WordPress melalui browser:
http://192.168.100.58/wp-admin/install.php
