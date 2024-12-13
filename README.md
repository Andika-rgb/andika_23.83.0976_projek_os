# andika_23.83.0976_projek_os
andika hendrawan_23.83.0976_23TK01

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
    sudo chmod -R 755 /var/www/your-website
    
