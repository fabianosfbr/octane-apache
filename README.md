#### Habilitar módulos do apache

sudo a2enmod proxy
sudo a2enmod proxy_http
sudo a2enmod proxy_ajp
sudo a2enmod rewrite
sudo a2enmod deflate
sudo a2enmod headers
sudo a2enmod proxy_balancer
sudo a2enmod proxy_connect
sudo a2enmod proxy_html

#### Configurar diretivas no virtual host para encaminhamento das portas

# Apache Conf
<VirtualHost *:80>

  ServerName fiscaut.com.br

	ServerAdmin suporte@fiscaut.com.br
	DocumentRoot /var/www/fiscaut.com.br/public

	ProxyPreserveHost On

  ProxyPass / http://127.0.0.1:8089/
  ProxyPassReverse / http://127.0.0.1:8089/
  
  <Directory /var/www/app/public>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All
        Require all granted
  </Directory>



	ErrorLog ${APACHE_LOG_DIR}/fiscaut.com.br.error.log
	CustomLog ${APACHE_LOG_DIR}/fiscaut.com.br.access.log combined


</VirtualHost>


#### Configuração do supervisor

# Supervisor Config
[program:fiscaut_app]
process_name=%(program_name)s
command=php /var/www/fiscaut.com.br/artisan octane:start --server=swoole --max-requests=1000 --port=8089
autostart=true
autorestart=true
user=ubuntu
redirect_stderr=true
stdout_logfile=/tmp/octane_app.log
stopwaitsecs=3600

# Restart Supervisor
sudo supervisorctl reread

sudo supervisorctl update

sudo supervisorctl start fiscaut_app
