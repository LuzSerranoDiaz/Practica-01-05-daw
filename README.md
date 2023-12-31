# Practica-01-05-daw

# Creacion de instancia en AWS

![aws](https://github.com/LuzSerranoDiaz/Practica-01-05-daw/assets/125549381/5efad519-0b5d-49fb-84b0-920bee02954a)

Se crea una instancia de Ubuntu 23.04 con sabor t2.micro (la gratuita) para que no haya errores con la memoria ram.

Se utiliza el par de claves generadas en por aws.

# Documento tecnico practica 1 despliegue de aplicaciones web DAW

En este documento se presentará los elementos para instalar LAMP, junto otras herramientas y modificaciones.

## Install_lamp.sh
```bash
#!/bin/bash
 
#Para mostrar los comandos que se van ejecutando
set -ex

#Actualizamos la lista de repositorios
apt update
#Actualizamos los paquetes del sistema
#apt upgrade -y

#Instalamos el servidor APACHE
sudo apt install apache2 -y

#Instalamos MYSQL SERVER
apt install mysql-server -y

#Instalar php 
sudo apt install php libapache2-mod-php php-mysql -y

#Copiamos archivo de configuracion de apache
cp ../conf/000-default.conf /etc/apache2/sites-available

#Reiniciamos servicio apache
systemctl restart apache2

#Copiamos el archivo de prueba de PHP
cp ../php/index.php /var/www/html

#Cambiamos usuario y propietario de var/www/html
chown -R www-data:www-data /var/www/html
```
En este script se realiza la instalación de LAMP en la última version de **ubuntu server** junto con la modificación del archivo 000-default.conf, para que las peticiones que lleguen al puerto 80 sean redirigidas al index encontrado en /var/www/html
### Como ejecutar Install_lamp.sh
1. Abre un terminal
2. Concede permisos de ejecución
 ```bash
 chmod +x install_lamp.sh
 ```
 o
 ```bash
 chmod 755 install_lamp.sh
 ```
 3. Ejecuta el archivo
 ```bash
 sudo ./install_lamp.sh
 ```
## .nev
```bash
# Variables de configuración
#-----------------------------------------------------------------------
CERTIFICATE_EMAIL=demo@demo.es
CERTIFICATE_DOMAIN=practica-15.ddns.net
#-----------------------------------------------------------------------
```
## setup_letsencrypt_certificate.sh
```bash
#!/bin/bash
 
# Para mostrar los comandos que se van ejecutando (x) y parar en error(e)
set -ex

# Actualizamos la lista de repositorios
 apt update
# ACtualizamos los paquetes del sistema
# apt upgrade -y
source .env
```
Se realizan los pasos premeditarios:
1. Actualizar repositorios
2. Se importa .env
```bash
#instalacion actualizacion snapd
sudo snap install core; sudo snap refresh core

#Eliminar instalaciones previas
sudo apt remove certbot

#Instalamos certbot con snpad
sudo snap install --classic certbot

#Un alias para el comando certbot
sudo ln -fs /snap/bin/certbot /usr/bin/certbot
```
1. Realizamos la instalación y actualización de snapd
2. Borramos versiones anteriores para que no de error si se tiene que ejecutar más de una vez
3. Utilizamos snapd para instalar el cliente de certbot
4. Y le damos un alias o link utilizando el comando `ln`
```bash
#certificado y configuramos el servidor web apache

sudo certbot \
    --apache \
    -m $CERTIFICATE_EMAIL \
    --agree-tos \
    --no-eff-email \
    -d $CERTIFICATE_DOMAIN \
    --non-interactive
```
Con el comando `certbot --apache` realizamos el certificado y con estos siguientes parametros automatizamos el proceso:
* `-m $CERTIFICATE_EMAIL` : indicamos la direccion de correo que en este caso es `demo@demo`
* `--agree-tos` : indica que aceptamos los terminos de uso
* `--no-eff-email` : indica que no queremos compartir nuestro email con la 'Electronic Frontier Foundation'
* `-d $CERTIFICATE_DOMAIN` : indica el dominio, que en nuestro caso es 'practica-15.ddns.net', el dominio conseguido con el servicio de 'no-ip'
* `--non-interactive` : indica que no solicite ningún tipo de dato de teclado.

