# Practica-01

## Instalación de phpMyAdmin

### Instalación de MySQL Server
sudo apt-get install mysql-server
### Instalación de módulos PHP
sudo apt-get install php libapache2-mod-php php-mysql
### Descargamos phpmyadmin dentro de la carpeta /var/www/html mediante git
sudo git clone https://github.com/phpmyadmin/phpmyadmin.git
### Descargamos composer.phar desde el sitio ofical de composer y lo introducimos dentro de la carpeta /var/www/html/phpmyadmin
sudo mv /home/luismi/composer.phar  /var/www/html/phpmyadmin
### Eliminamos dos paquetes en el archivo composer.json dentro de "required-dev" al final del archivo
sudo nano composer.json
### Ejecutamos composer para actulizar dependencias y instalar phpmyadmin
php composer.phar update
### Cambiamos propietario de la carpeta /var/www/html/phpmyadmin
sudo chown www-data:www-data phpmyadmin/ -R

## Instalación de adminer

### Descargamos Adminer dentro de /var/www/html/adminer y cambiamos el propietario del directorio
sudo mkdir adminer
git clone https://github.com/vrana/adminer/releases/tag/v4.6.3/adminer-4.6.3-en.php.git
sudo chown www-data:www-data adminer/ -R

## Instalación de un analizador de logs para Apache Server (GoAccess)

echo "deb http://deb.goaccess.io/ $(lsb_release -cs) main" | sudo tee -a /etc/apt/sources.list.d/goaccess.list
wget -O - https://deb.goaccess.io/gnugpg.key | sudo apt-key add -
sudo apt-get update
sudo apt-get install goaccess

## Control de acceso a un directorio con .htaccess

### Creamos el directorio stats.

mkdir /var/www/html/stats

### Lanzamos el proceso de goaccess en background para generar los informes en segundo plano.

sudo goaccess /var/log/apache2/access.log -o /var/www/html/stats/index.html --log-format=COMBINED --real-time-html &

### Creamos el archivo de contraseñas para el usuario que accederá al directorio stats. El archivo de contraseñas lo guardamos en un directorio seguro. En nuestro caso lo podemos guardar en /home/usuario.

sudo htpasswd -c /home/usuario/.htpasswd usuario

### Creamos el archivo .htaccess en el directorio que queremos proteger con usuario y contraseña. En nuestro caso lo vamos a crear en el directorio /var/www/html/stats/.htaccess.

sudo /var/www/html/stats/.htaccess
El contenido del archivo .htaccess será el siguiente:

AuthType Basic
AuthName "Restricted Content"
AuthUserFile /home/usuario/.htpasswd
Require valid-user

### Editamos el archivo configuración de Apache.

sudo nano /etc/apache2/sites-enabled/000-default.conf
Añadimos la siguiente sección dentro de las etiquetas de <VirtualHost *:80> y </VirtualHost>.

<Directory "/var/www/html/stats">
  Options Indexes FollowSymLinks
  AllowOverride All
  Require all granted
</Directory>

### Reiniciamos el servicio de Apache.

sudo /etc/init.d/apache2 restart

#### añado nuevo pas
