# Instalacion-WordPress
## Vagrant
Para instalar WordPress tendremos que usar `Vagrant`, creamos un directorio con cualquier nombre. Toda la configuracion del proyecto se escrirá en el fichero `Vagrantfile`.
```console
smx2b@turing-116:~$ mkdir Ejemplo
smx2b@turing-116:~$ cd Ejemplo
```

Podemos crear directamente el fichero `Vagrantfile` con el siguiente comando: `vagrant init <box>`.
```console
smx2b@turing-116:~$ vagrant init ubuntu/jammy64
smx2b@turing-116:~$ ll
```

Una vez creado, levantamos la infraestructura con el siguiente comando y parametro: `vagrant up` `--provider=virtualbox`.
```console
smx2b@turing-116:~$ vagrant up --provider=virtualbox
```
Una vez montado ya podremos usar `ssh`, para entrar a la MV que ha montado el comando anterior.
```console
smx2b@turing-116:~$ vagrant ssh
smx2b@turing-116:~$ ll /vagrant
```
### Otros comandos:
- `vagrant status`: Nos muestra informacion sobre el estado de las MVs.
- `vagrant halt`: Parar las MVs
- `vagrant suspend`: Suspender las MVs
- `vagrant up`: Encender las MVs
- `vagrant destroy`: Borrar las MVs

## Instalacion y configuracion de aplicaciones web
Para instalar una aplicacion web, necesitamos su codigo fuente y traerlo al directorio de nuestro servidor de aplicaciones, en este caso apache2. Cuanto instalas apache2 se crea una carpeta en `/var/www/html`. Si llevamos nuestra aplicacion al directorio `/var/www/html` tendremos acceso a nuestra aplicacion con la direccion `http://localhost`.

### Instalacion Apache2, Mysql, librerias
- Dentro de la MV, actualizaremos la maquina con los comandos: `apt update` y `apt upgrade`.
```console
smx2b@turing-116:~$ apt update
smx2b@turing-116:~$ apt upgrade
```
1. Instalacion `apache2`.
```console
smx2b@turing-116:~$ apt install -y apache2
```
2. Instalacion del servidor de bases de datos `mysql-server`.
```console
smx2b@turing-116:~$ apt install -y mysql-server
```
3. Instalacion de librerias `php` (Lenguaje principal que usan las aplciaciones).
```console
smx2b@turing-116:~$ apt install -y php libapache2-mod-php
smx2b@turing-116:~$ apt install -y php-fpm php-common php-mbstring php-xmlrpc php-soap php-gd php-xml php-intl php-mysql php-cli php-ldap php-zip php-curl
```
4. Reinicio servidor apache2
```console
smx2b@turing-116:~$ systemctl restart apache2
```
- RECORDAR SER ROOT

### Configuracion MySQL
#### Accedemos a la consola de MySQL
Desde una terminal ejecutamos el comando siendo `root`.
```console
smx2b@turing-116:~$ mysql
```
#### Creacion de la BBDD
Dentro de la consola, ejecutaremos el comando:
```console
CREATE DATABASE bbdd;
```
Este comando nos creara una base de datos con el nombre `bbdd`.
#### Creacion de un usuario
```console
CREATE USER 'usuario'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```
#### Assignamos permisos al usuario y salimos
```console
GRANT ALL ON bbdd.* to 'usuario'@'localhost';
```
```console
exit
```
#### Probamos la conexion a la base de datos
Desde una terminal con un usuario sin permisos, tendriamos que ser capaces de conectarnos introduciendo la contraseña.
```console
smx2b@turing-116:~$ mysql -u usuario -p
```
#### Mover archivo del servidor en la nube (Nextcloud)
Vamos a la pagina oficial y nos descargamos el `.zip` del servidor en la nube. Copia y pega el `.zip` en el directorio que hemos creado al principio del todo y ejecutamos este comando:
```console
smx2b@turing-116:~$ mv /vagrant/wordpress.zip /var/www/html/
```
#### Descomprimir el .zip
Una vez hecho todo esto, utilizaremos el siguiente comando:
```console
smx2b@turing-116:~$ unzip wordpress.zip
```
Copiaremos el directorio descompromido y lo borraremos.

#### Aplicacion de permisos a nuestras aplicaciones web
Dentro de `/var/www/html` daremos los permisos a los archivos descomprimidos.
```console
cd /var/www/html
chmod -R 775 .
chown -R root:www-data .
```
## Activar visibilidad de la web
Para hacer visible la web, tendremos que modificar algunos parametros en el archivo `vagrantfile`.
```console
vi vagrantfile
```
```console
# Create a forwarded port mapping which allows access to a specific port
# within the machine from a port on the host machine. In the example below,
# accessing "localhost:8080" will access port 80 on the guest machine.
# NOTE: This will enable public access to the opened port
config.vm.network "forwarded_port", guest: 80, host: 8080
```
```console
# Create a public network, which generally matched to bridged network.
# Bridged networks make the machine appear as another physical device on
# your network.
config.vm.network "public_network"
```