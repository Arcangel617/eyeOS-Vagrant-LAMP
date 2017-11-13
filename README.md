# Instalación de eyeOS utilizando Vagrant, Ubuntu 12.04 (precise), Apache, PHP5 y MySQL  
Ésta guía fue realizada con el fin de demostrar la existencia de sistemas operativos en la nube. Cabe aclarar que el proyecto
### ¿Qué es eyeOS?
eyeOS es una plataforma de nube privada con una interfaz de escritorio basada en la web. Comúnmente llamado escritorio en la nube por su interfaz única, eyeOS proporciona un escritorio completo desde la nube con gestión de archivos, herramientas de gestión de la información personal, herramientas colaborativas y aplicaciones de la compañía.

Se trata de un nuevo concepto en almacenaje virtual, el cual se considera como revolucionario al ser un servicio clave para el Web 2.0 ya que dentro de una web que combina el poder del actual HTML, PHP, AJAX y JavaScript para crear un entorno gráfico de tipo escritorio.

La diferencia entre otros entornos escritorio al hacer posible iniciar el escritorio eyeOS y todas sus aplicaciones desde un navegador web. No se requiere instalar ningún software adicional, ya que solo se necesita un navegador que soporte AJAX, Java y Adobe Flash (dependiendo de las aplicaciones que se deseen ejecutar). [Wikipedia](https://es.wikipedia.org/wiki/EyeOS)

#### Consideraciones previas
- Se asume que [Vagrant](https://www.vagrantup.com/) ya está instalado en el sistema host. 
- Ubuntu 12.04 dejó de tener soporte en Abril de 2017
- eyeOS tambien dejó de tener soporte

#### Requerimientos
- Vagrant
- Git

### Clonar el repositorio
```
$ git clone https://github.com/Arcangel617/eyeOS-Vagrant-LAMP
```
### Inicializamos nuestra máquina virtual
```
$ cd eyeOS-Vagrant-LAMP && vagrant up
```
Una vez que nuestra máquina esté corriendo accedemos a ella de la mediante ssh de la siguiente forma:
```
$ vagrant ssh
```
Si todo fue bien veremos algo como esto:
```
Welcome to Ubuntu 12.04.5 LTS (GNU/Linux 3.2.0-126-virtual x86_64)

 * Documentation:  https://help.ubuntu.com/

 System information disabled due to load higher than 1.0

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

New release '14.04.5 LTS' available.
Run 'do-release-upgrade' to upgrade to it.



This Ubuntu 12.04 LTS system is past its End of Life, and is no longer
receiving security updates.  To protect the integrity of this system, it’s
critical that you enable Extended Security Maintenance updates:
 * https://www.ubuntu.com/esm

Last login: Mon Nov 13 09:06:15 2017 from 10.0.2.2
vagrant@vagrant-ubuntu-precise-64:~$ 

```

### Instalar LAMP stack
Una vez que hayamos accedido procedemos a instalar los siguientes paquetes 
```
$ sudo apt-get install language-pack-es
```
```
$ sudo apt-get install lamp-server^
```
> Nota: recordar agregar el ^ al final

#### Instalación de paquetes adicionales para PHP
```
$ sudo apt-get install openoffice.org php5-gd php5-imagick libimage-exiftool-perl php5-sqlite php-pear php5-dev python-uno php5-mcrypt php5-curl zip unzip
```

#### Componentes extra
```
$ sudo pecl install uploadprogress
```

### Preparar eyeOS para la instalación
Tendremos que crear un directorio /var/www
```
$ sudo mkdir /var/www/eyeOS
```
Descomprimimos el archivo eyeos-2.5.tar.gz en dicho directorio de la siguiente manera:
```
$ sudo tar xvfz /vagrant/eyeos-2.5.tar.gz -C /var/www/eyeOS/
```
Le asignamos los siguientes permisos:
```
$ sudo chown -R www-data:www-data /var/www/eyeOS/
$ sudo chmod 750 -R /var/www/eyeOS/
```

### Instalación phpMyAdmin
Para gestionar la base de datos utilizaremos phpMyAdmin. Para ellos en la consola escribimos:
```
sudo apt-get install phpmyadmin
```
Una vez que termine la instalación ejecutamos lo siguiente:
```
$ echo "Include /etc/phpmyadmin/apache.conf" | sudo tee -a /etc/apache2/apache2.conf 
```
y reiniciamos el servicio de apache:
```
$ sudo service apache2 restart
```
> Nota: se asume que se sabe crear una base de datos con phpMyAdmin. El nombre de la base de datos queda a criterio de cada uno. En mi caso utilice como nombre "eyeos" (ya que el proceso de instalación del SO lo sugiere).

#### Configuración de apache
Habilitamos el siguiente módulo de apache:
```
$ sudo a2enmod rewrite
```
Ahora debemos editar una línea del archivo /etc/apache2/sites-available/default
```
$ sudo nano /etc/apache2/sites-available/default
```
Una vez allí hay que buscar una sección llamada <directory /var/www/>. Allí tendremos que modificar la línea "AllowOverride None" por "AllowOverride All". Guardamos los cambios y reiniciamos el servicio:
```
$ sudo service apache2 restart
```
Ahora debemos asegurarnos que los siguientes parámetros del archivo php.ini queden de la siguiente manera:
```
$ sudo nano /etc/php5/apache2/php.ini 
```
> error_reporting = E_ALL & ~E_NOTICE
> display_errors = Off
> max_execution_time = 30
> max_input_time = 60
> memory_limit = 128M
> post_max_size = 200M
> upload_max_filesize = 100M
> allow_url_fopen = On
> disable_functions =
> safe_mode = Off
> short_open_tag = On
> magic_quotes_runtime = Off
> file_uploads = On


#### Instalación de eyeOS
Para instalar eyeOS tendremos que dirigirnos a nuestro navegador y seguir los pasos del proceso de instalación:
> http://localhost/eyeOS/Install
