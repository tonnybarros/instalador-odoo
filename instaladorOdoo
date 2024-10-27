#!/bin/bash

# Variáveis de instalação
ODOO_USER="odoo"
ODOO_HOME="/opt/$ODOO_USER"
ODOO_VERSION="16.0"  # Ajuste para a versão desejada
DOMAIN_NAME="seu.dominio"  # Substitua pelo seu domínio
ADMIN_EMAIL="seuemail@gmail.com"  # Substitua pelo e-mail do administrador para SSL

# Atualizando pacotes
echo "Atualizando pacotes..."
sudo apt update && sudo apt upgrade -y

# Instalando dependências
echo "Instalando dependências..."
sudo apt install -y python3-pip build-essential wget git python3-dev libxml2-dev libxslt1-dev libevent-dev libsasl2-dev libldap2-dev libjpeg-dev libpq-dev libjpeg8-dev liblcms2-dev libblas-dev libatlas-base-dev libssl-dev libffi-dev xz-utils libpcre3 libpcre3-dev libtiff5-dev libfreetype6-dev libwebp-dev zlib1g-dev libopenjp2-7-dev libharfbuzz-dev libfribidi-dev libxcb1-dev libpq-dev

# Instalando PostgreSQL
echo "Instalando PostgreSQL..."
sudo apt install -y postgresql
sudo -u postgres createuser --superuser $ODOO_USER
sudo -u postgres createdb $ODOO_USER

# Criando usuário do sistema
echo "Criando usuário Odoo..."
sudo adduser --system --home=$ODOO_HOME --group $ODOO_USER

# Baixando o Odoo
echo "Baixando o Odoo..."
sudo git clone --depth 1 --branch $ODOO_VERSION https://www.github.com/odoo/odoo $ODOO_HOME

# Configurando ambiente Python e instalando dependências Odoo
echo "Configurando ambiente Python..."
sudo pip3 install -r $ODOO_HOME/requirements.txt

# Criando arquivo de configuração
echo "Configurando o Odoo..."
sudo cp $ODOO_HOME/debian/odoo.conf /etc/odoo.conf
sudo chown $ODOO_USER: /etc/odoo.conf
sudo chmod 640 /etc/odoo.conf
sudo bash -c "echo '[options]
admin_passwd = admin
db_host = False
db_port = False
db_user = $ODOO_USER
db_password = False
addons_path = $ODOO_HOME/addons
logfile = /var/log/odoo/odoo.log' > /etc/odoo.conf"

# Configurando o serviço Odoo
echo "Configurando o serviço do Odoo..."
sudo bash -c "echo '[Unit]
Description=Odoo
Documentation=https://www.odoo.com
[Service]
Type=simple
User=$ODOO_USER
ExecStart=/usr/bin/python3 $ODOO_HOME/odoo-bin -c /etc/odoo.conf
[Install]
WantedBy=multi-user.target' > /etc/systemd/system/odoo.service"

# Reiniciando e habilitando serviço
echo "Iniciando serviço do Odoo..."
sudo systemctl daemon-reload
sudo systemctl enable --now odoo

# Instalando e configurando Nginx
echo "Instalando e configurando Nginx..."
sudo apt install -y nginx
sudo bash -c "echo 'server {
    listen 80;
    server_name $DOMAIN_NAME;

    access_log /var/log/nginx/odoo-access.log;
    error_log /var/log/nginx/odoo-error.log;

    proxy_buffers 16 64k;
    proxy_buffer_size 128k;

    location / {
        proxy_pass http://127.0.0.1:8069;
        proxy_set_header Host \$host;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto \$scheme;
    }

    location /longpolling {
        proxy_pass http://127.0.0.1:8072;
    }

    gzip_types text/css text/scss text/plain text/xml application/xml application/json application/javascript;
    gzip on;
}' > /etc/nginx/sites-available/odoo"
sudo ln -s /etc/nginx/sites-available/odoo /etc/nginx/sites-enabled/
sudo systemctl restart nginx

# Instalando Certbot para SSL
echo "Instalando Certbot e configurando SSL..."
sudo apt install -y certbot python3-certbot-nginx
sudo certbot --nginx -d $DOMAIN_NAME --non-interactive --agree-tos -m $ADMIN_EMAIL

echo "Configuração completa! Odoo está disponível em https://$DOMAIN_NAME"
