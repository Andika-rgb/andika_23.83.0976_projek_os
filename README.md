# andika_23.83.0976_projek_os
andika hendrawan_23.83.0976_23TK01

# Update sistem
apt update && apt upgrade -y ||

# 1. INSTALASI NGINX (Web Server)
    apt install nginx -y || error_exit "Gagal instal Nginx"
    systemctl enable nginx
    systemctl start nginx

# 2. INSTALASI POSTGRESQL (Database)
    apt install postgresql postgresql-contrib -y || error_exit "Gagal instal PostgreSQL" 
    systemctl enable postgresql 
    systemctl start postgresql

# Konfigurasi PostgreSQL
    sudo -u postgres psql <<EOF 
    CREATE DATABASE multiservice_db; 
    CREATE USER multiuser WITH PASSWORD 'passwordkuat123'; 
    GRANT ALL PRIVILEGES ON DATABASE multiservice_db TO multiuser; 
    EOF

# 3. INSTALASI PYTHON FLASK (Backend API) 
    apt install python3-pip python3-venv -y 
    python3 -m venv /opt/serverapp 
    source /opt/serverapp/bin/activate 
    pip install flask psycopg2-binary gunicorn

# Buat contoh Flask App
    mkdir -p /opt/serverapp/src 
    cat > /opt/serverapp/src/app.py <<FLASK 
    from flask import Flask, jsonify 
    import psycopg2 
    
    app = Flask(__name__) 
    
    @app.route('/status') 
    def service_status(): 
        return jsonify({ 
            "status": "aktif", 
            "services": ["nginx", "postgresql", "flask"] 
        }) 
    
    if __name__ == '__main__': 
        app.run(host='0.0.0.0', port=5000) 
    FLASK

# 4. INSTALASI GRAFANA (Monitoring)
    apt-get install -y software-properties-common 
    wget -q -O - https://packages.grafana.com/gpg.key | apt-key add - 
    add-apt-repository "deb https://packages.grafana.com/oss/deb stable main" 
    apt-get update 
    apt-get install grafana -y || error_exit "Gagal instal Grafana" 
    systemctl enable grafana-server 
    systemctl start grafana-server 

# 5. INSTALASI KEYCLOAK (Autentikasi)
    apt install default-jdk -y 
    wget https://github.com/keycloak/keycloak/releases/download/21.1.1/keycloak-21.1.1.tar.gz 
    tar -xvzf keycloak-21.1.1.tar.gz -C /opt/ 
    mv /opt/keycloak-21.1.1 /opt/keycloak

# Buat direktori website
    mkdir -p /var/www/multiservice-website/html
    mkdir -p /var/www/multiservice-website/backend

# Buat file HTML
    cat > /var/www/multiservice-website/html/index.html <<HTML 
    <!DOCTYPE html>
    <html lang="id">
    <head>
        <meta charset="UTF-8">
        <title>Multi-Service Dashboard</title>
        <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.2.3/dist/css/bootstrap.min.css" rel="stylesheet">
    </head>
    <body>
        <div class="container mt-5">
            <h1>Multi-Service Dashboard</h1>
            <div class="row">
                <div class="col-md-4">
                    <div class="card">
                        <div class="card-header">Nginx</div>
                        <div class="card-body" id="nginx-status">Status: Aktif</div>
                    </div>
                </div>
                <div class="col-md-4">
                    <div class="card">
                        <div class="card-header">PostgreSQL</div>
                        <div class="card-body" id="postgres-status">Status: Terhubung</div>
                    </div>
                </div>
                <div class="col-md-4">
                    <div class="card">
                        <div class="card-header">Flask API</div>
                        <div class="card-body" id="flask-status">Status: Berjalan</div>
                    </div>
                </div>
            </div>
        </div>
    </body>
    </html>
    HTML

# Konfigurasi Nginx untuk website
    log_message "Mengonfigurasi Nginx..."
    cat > /etc/nginx/sites-available/multiservice <<NGINX

    server {
      listen 80;
      server_name localhost;

    root /var/www/multiservice-website/html;
    index index.html;

    location / {
        try_files \$uri \$uri/ =404;
    }

    location /api {
        proxy_pass http://127.0.0.1:5000;
        proxy_set_header Host \$host;
        proxy_set_header X-Real-IP \$remote_addr;
    }

    location /grafana {
        proxy_pass http://127.0.0.1:3000;
    }

    location /auth {
        proxy_pass http://127.0.0.1:8080;
    }
    }
    NGINX

# Aktifkan konfigurasi Nginx
    ln -s /etc/nginx/sites-available/multiservice /etc/nginx/sites-enabled/
    nginx -t
    systemctl restart nginx

# Konfigurasi Firewall
    log_message "Mengonfigurasi Firewall..."
    ufw allow 'Nginx Full'
    ufw enable

# Buat systemd service untuk Flask app
    cat > /etc/systemd/system/multiservice-flask.service <<SYSTEMD
    [Unit]
    Description=Multi Service Flask App
    After=network.target
    
    [Service]
    User=root
    WorkingDirectory=/opt/serverapp/src
    ExecStart=/opt/serverapp/bin/gunicorn --bind 0.0.0.0:5000 app:app
    Restart=always
    
    [Install]
    WantedBy=multi-user.target
    SYSTEMD

# Reload systemd dan start service
    systemctl daemon-reload
    systemctl enable multiservice-flask
    systemctl start multiservice-flask
