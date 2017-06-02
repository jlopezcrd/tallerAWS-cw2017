# Taller AWS CW2017
Taller realizado para el Congreso Web de Zaragoza 2017

**¡ Falta terminar de documentar !**

## Conexión al servidor
```bash

ssh -i ~/.ssh/TU-USUARIO/TU-LLAVE.pem

```

## Instalación del servidor
```bash

# Actualizamos caches de paqueteria
apt update

# Instalamos el servidor web
apt install apache2 libapache2-mod-php7.0

# Instalamos php y sus dependencias
apt install php7.0 \
    php-gettext \
    php-mysql \
    php-curl \
    php-pear \
    php-imagick \
    php-memcache \
    php7.0-mysql \
    php7.0-mcrypt \
    php7.0-curl \
    php7.0-gd \
    php7.0-intl \
    php7.0-imap \
    php7.0-mcrypt \
    php7.0-pspell \
    php7.0-recode \
    php7.0-sqlite3 \
    php7.0-tidy \
    php7.0-xmlrpc \
    php7.0-xsl \
    php7.0-mbstring 

# Reiniciamos el servidor web
systemctl restart apache2

# Instalamos el servidor mysql
## Cuando lo instalamos nos pregunta la contraseña para el superuser
apt install mysql-server

# Nos conectamos al servidor mysql
## Introducimos nuestra password interactivamente
mysql -u root -p

# Creamos un usuario y su password
CREATE USER 'wordpress-user'@'localhost' IDENTIFIED BY 'TU-PASSWORD';

# Creamos una base de datos
CREATE DATABASE `wordpress-db`;

# Concedemos los permisos al usuario a nuestra base de datos
GRANT ALL PRIVILEGES ON `wordpress-db`.* TO "wordpress-user"@"localhost";

# Limpiamos permisos;
FLUSH PRIVILEGES;

# Salimos de mysql
exit

```

## Configuramos Wordpress
```bash

# Nos situamos en el directorio de apache
cd /var/www/html

# Eliminamos fichero por defecto
rm -rf index.html

# Descargamos la ultima version de wp
wget https://wordpress.org/latest.tar.gz

# Desempaquetamos el wp
tar -xzf latest.tar.gz

# Listamos directorio
ls

# Entramos en la carpeta del wp
cd wordpress/

# Copiamos el fichero de configuracion para tener una copia
cp wp-config-sample.php wp-config.php

# Editamos el fichero de configuracion con el editor que prefiramos
vim wp-config.php
nano wp-config.php

# Editamos las constantes de entorno 
define('DB_NAME', 'wordpress-db');
define('DB_USER', 'wordpress-user');
define('DB_PASSWORD', 'TU-PASSWORD');

# Generamos nuevas keys para wp (NO USAR ESTAS POR SEGURIDAD)
https://api.wordpress.org/secret-key/1.1/salt/

define('AUTH_KEY',         '|7*M}UXcl&q=cOz@RVbqGN@LB+b-we*j{jefXA+>R3pk7U7`6`6}MTh|/:OM-TJT');
define('SECURE_AUTH_KEY',  'l]H ;0H3rWY+j7Zn!Q2~:dr:+AGZjJ>ax+4WeD*EpfUVhhL&ey8F%t{A(JR:E[hC');
define('LOGGED_IN_KEY',    'H^+kRR,^aXUJHPG*+Jh=W.scB[%<m|P*XI)d9EEz,40EiJ6(puw| |oMJ9fF39^a');
define('NONCE_KEY',        'GIA?oEM,6EGSKU|eRxcowgNL%Y+7SNW:3m8W1sc+i78ZWIOcP4REkPm_p_[-5%=K');
define('AUTH_SALT',        '0mp9^|ILJR--[v3M:hX#uL`IDtB{E8v Sq5ESy|e#dqU$:Sy%D*8K;mCTkP]2G1U');
define('SECURE_AUTH_SALT', '2|rFA%F=9>}l#r8+PwVZ6!.kUT[peQ:+ ?[O4hOlHQWn$a[;sl5aYNn8h7uhP^_ ');
define('LOGGED_IN_SALT',   'l~xOu8]q}3XEU>0Tg}:|:_|gpsV&@DywM!xsL/?bnn?qW+Dt`FE(I6<`$Y<y^;sN');
define('NONCE_SALT',       '2%&1~0!sN+sd0:c_!,dCc.D0*yEI;M)$I_a;uUW`M8snR2B]zjwQ1wmD&{EKgH@-');


# Movemos el contenido de la carpeta de wordpress al directorio raiz de apache
mv * /var/www/html/

# Subimos un directorio
cd ..

# Eliminamos la carpeta wordpress
rm -rf wordpress

# Asignamos permisos correctos a nuestro wp
sudo chown -R www-data: /var/www/html

# En caso necesario (Si no se descomprime con los permisos adecuados)
sudo chmod 2775 /var/www
find /var/www -type d -exec sudo chmod 2775 {} \;
find /var/www -type f -exec sudo chmod 0664 {} \;

# Reiniciamos apache
service apache2 restart

```

## Instalamos el plugin W3 TOTAL CACHE
```bash

Entramos a la administración de wp:
http://TU-DOMINIO/wp-login.php

Nos situamos en el apartado plugins
Pulsamos en add new
Buscamos W3 Total CACHE y lo instalamos

Una vez instalado y activado vamos a Performance > General Settings
Configuramos el CDN con Amazon Simple Storage Service (S3)

Despues vamos a CDN y configuramos:
Access Key ID: Tu key de IAM de AWS
Secret Key: Tu secret key de IAM de AWS
Bucket: Tu bucket de AWS

Y probamos el test, si es correcto podemos empezar a subir todos los attachments, included files, theme files, minify files, custom files.

Si queremos tener total control podemos hacer una politica:

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:*",
            "Resource": [
                "arn:aws:s3:::TU-BUCKET/*"
            ]
        }
    ]
}

Y con esto estaría nuestro WP usando todo lo estatico en S3

El siguiente paso sería usar el CDN CloudFront (Para esto necesitamos otro taller)

```

## Creación de imagen base

## Creación de Balanceador

## Creación de RDS MySql