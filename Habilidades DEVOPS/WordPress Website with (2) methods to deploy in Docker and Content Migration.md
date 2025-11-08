## WordPress Website with two methods to deploy in Docker and Content Migration

### Objetive:
El Objetivo de este laboratorio, busca explicar y  crear un  sitio web Wordpress implementado a travez de Docker (2 capas), donde a partir de dockerfiles, que se gestiona a travez de consola OrbStack (docker engine), levantando el sitio web en localhost. Posteriormente, migrar el contenido de mi sitio Web profesional, existente en AWS Cloud.
Adicional, explorando el apoyo del Asistente de Inteligencia Artificial Amazon Q.

### Application Architecture:
El sitio web se compone de (2) capas:  BASES DE DATOS - FRONTEND

**CONTENEDORES:**
- BASE DE DATOS: MYSQL 
	Contenedor: database   puerto:3306 (puerto interno contenedor)
- FRONTEND:  PHP - APP Legacy WORDPRESS
	Contenedor: wordpress     puerto:80 (puerto interno contenedor)

**PRE-REQUISITOS:**
En un ambiente local (tu maquina) necesitas;

-   Motor de DOCKER (engine docker) para crear esta solución de contenedores, entonces se puedes instalar y usar Docker Desktop o OrbStack  

-   Preferiblemente,  usar una Interfaz (IDE) de desarrollo como VSCODE. Si no puede usar tu consola o terminal para interactuar con la shell (linea de comando)

-   Crear un carpeta para tu proyecto y abrir o relacionar como workspace en VSCODE, con una subcarpeta para wordpress y otra para base de datos. Posterior, crearemos los demás archivos 

Exiten, dos formas de implementar:

**A. Implementando cada componente, por aparte**

-   Crear la **RED en ambiente docker**, que va a relacionar los (2) contenedores
```
> docker network create mysitewp-network
> docker network list
```
-   Crear archivo **dockerfile BASE DE DATOS(MYSQL) y FRONTEND (PHP -WORDPRESS)**, por aparte

BASE DE DATOS(MYSQL)
```
# EN BASE A IMAGEN DE MYSQL
FROM mysql:8.0

# Variables de entorno para MySQL
ENV MYSQL_ROOT_PASSWORD=rootpassword
ENV MYSQL_DATABASE=wordpress
ENV MYSQL_USER=wpuser
ENV MYSQL_PASSWORD=wppassword
# Exponer el puerto 3306
EXPOSE 3306
```
FRONTEND (PHP -WORDPRESS)
```
# EN BASE A IMAGEN DE WORDPRESS
FROM wordpress:latest

# corre script docker-php-ext-install 
# ademas de instalar las extensiones PHP adicionales ( mysqli, pdo, pdo_mysql)
RUN docker-php-ext-install mysqli pdo pdo_mysql

# Copiar configuración PHP.ini personalizada para aumentar limites de subida y tiempo de ejecucion
COPY php.ini /usr/local/etc/php/conf.d/custom.ini

# Copiar script de entrada personalizado, para asegurar las variables de entorno
COPY entrypoint.sh /usr/local/bin/
RUN chmod +x /usr/local/bin/entrypoint.sh

# Usar script personalizado como entrypoint
ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]
CMD ["apache2-foreground"]

# Exponer el puerto 80
EXPOSE 80
```
-   Crear la **IMAGEN  a partir del archivo dockerfile** para cada uno. Se ubica en cada directorio del proyecto 
```
# para BASE DATOS
> docker build -t ima-mysite-db .  
# para APP WORDPRESS   
> docker build -t ima-mysite-wp .    
```
-   Crear o **correr (RUN) los contenedores** con todos los parámetros

**para BASE DATOS**
```
> docker run -d \
  --name bases-de-datos \
  --network mysitewp-network \
  -e MYSQL_ROOT_PASSWORD=rootpassword \
  -e MYSQL_DATABASE=wordpress \
  -e MYSQL_USER=wpuser \
  -e MYSQL_PASSWORD=wppassword \
  -p 3306:3306 \
  ima-mysite-db
```
**para APP WORDPRESS**
```
> docker run -d \
  --name wordpress \
  --network mysitewp-network \
  -p 8080:80 \
  -e WORDPRESS_DB_HOST=bases-de-datos:3306 \
  -e WORDPRESS_DB_USER=wpuser \
  -e WORDPRESS_DB_PASSWORD=wppassword \
  -e WORDPRESS_DB_NAME=wordpress \
  -v $(pwd)/wordpress/entrypoint.sh:/usr/local/bin/entrypoint.sh \
  -v $(pwd)/wordpress/php.ini:/usr/local/etc/php/conf.d/custom.ini \
  ima-mysite-wp
  ```

**B.  Implementando a partir de la ejecución de un archivo docker-compose.yaml**

**Crear los dockerfiles**
-   BASE DE DATOS(MYSQL):  En la sub carpeta Database, crear un archivo para dockerfile, el cual tiene los siguientes comandos y comentarios

-   FRONTEND (PHP -WORDPRESS):  En la sub carpeta wordpress, crear un archivo para dockerfile, el cual tiene los siguientes comandos y comentarios

-   Crear el **archivo DOCKER-COMPOSE (docker-compose.yaml)**, el cual define la relación y la dependencia de los contenedores entre si, para implementarlos

```
version: '3.8'
# Este archivo docker-compose, configura los servicios para relacionar los contenedores
# de la base de datos MySQL y el servidor web WordPress.
services:
# Servicio de base de datos MySQL
  database:
    build: ./database
    restart: always
# Asignar un volumen para persistencia de datos de la base de datos
    volumes:
      - db_data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: wppassword

# Servicio de WordPress
  wordpress:
    build: ./wordpress
    restart: always
    ports:
      - "8080:80"
# depends_on asegura que el contenedor de la base de datos se inicie antes que WordPress
    depends_on:
      - database
# Variables de entorno para conectar WordPress con la base de datos
    environment:
      WORDPRESS_DB_HOST: database:3306
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: wppassword
      WORDPRESS_DB_NAME: wordpress
# Asignar un volumen para persistencia de datos de WordPress
    volumes:
      - wp_data:/var/www/html
# Definir 2 volumenes para persistencia de data y archivo de wordpress y mysql
volumes:
  db_data:
  wp_data:
```
-   **Ejecutar solo docker-compose**, que se basa en lo dockerfile creados anteriormente para Bases de datos/aplicación wordpres
```
> docker-compose up -d
```
-   Se agrega en este proyecto los **archivos entrypoint.sh y php.ini** para configurar las variables de entorno a nivel de Wordpress que permiten ampliar la configuración de PHP, con el fin de usar el **plug-in de wordpress llamado ALL-in-One WP Migration** (instalar el modulo en wordpress) 

entrypoint.sh 
```
#!/bin/bash

# Aplicar configuración PHP
echo "upload_max_filesize = 128M" > /usr/local/etc/php/conf.d/custom.ini
echo "post_max_size = 128M" >> /usr/local/etc/php/conf.d/custom.ini
echo "memory_limit = 256M" >> /usr/local/etc/php/conf.d/custom.ini
echo "max_execution_time = 300" >> /usr/local/etc/php/conf.d/custom.ini
echo "max_input_time = 300" >> /usr/local/etc/php/conf.d/custom.ini

# Configurar variables de WordPress para wp-config.php
export WORDPRESS_CONFIG_EXTRA="
ini_set('upload_max_filesize', '128M');
ini_set('post_max_size', '128M');
ini_set('memory_limit', '256M');
ini_set('max_execution_time', 300);
ini_set('max_input_time', 300);
"

# Ejecutar el entrypoint original de WordPress
exec docker-entrypoint.sh "$@"
```
php.ini
```
upload_max_filesize = 128M
post_max_size = 128M
memory_limit = 256M
max_execution_time = 300
max_input_time = 300
```
-   El plug-in ALL-in-One WP Migration permite restablecer o importar un archivo tipo backup de sitio wordpress (ejemplo: mysite-cloud.wpress)  y migrar el contenido a esta nueva infraestructura local implementada con Docker. 

Finalmente, se **implementa el docker-compose.ymal** con comando 
```
docker-compose up -d
```
Para navegarlo a través de localhost puerto 8080
```
http://localhost:8080/
```

**Migracion Contenido Sitio WP**, anterior 

Una vez, activo el sitio http://localhost:8080/, se configura con usuario y password administrador de worpress.

-   Se instala el **plug-in ALL-in-One WP**, y se va opción import para cargar el **archivo mysite-cloud.wpress** (previo backup o exportación del sitio anterior wordpresss en cloud)

-   Se carga **archivo mysite-cloud.wpress**, donde se comprueba que la variables de entorno y para ampliar configuración de PHP (archivos entrypoint y php.ini), se ejecutaron y serán persistentes en el despliegue de los contenedores docker de esta solución.

Quedando migrado el sitio deplegado en contenedores en este caso, en ambiente localhost 

**Adicionales:**

**Gestion en consola OrbStack (engine de docker):**

-   Se observa los CONTENEDORES con su información
-   Se observa los VOLUMENES creados, con opción para exportar la data
-   Se observa las IMAGENES creadas de los contenedores
-   En consola OrbStack, permite acceder y manipular todos los artefactos (contenedores, imágenes, volúmenes),  creados 

**Uso y apoyo con Inteligencia Artificial mediante Amazon Q - modelo IA, Claude Sonnet 4.**. De uso a través de VSCODE.

### Technical Desing:

![Technical Desing](https://ocvpprofessional.cloud/wp-content/uploads/2025/11/WP-docker-Orbstack-AwsQ.png)
