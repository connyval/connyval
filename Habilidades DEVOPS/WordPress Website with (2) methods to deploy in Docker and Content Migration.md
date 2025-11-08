## WordPress Website with two methods to deploy in Docker and Content Migration

### Objetive:
El Objetivo de este laboratorio, busca explicar y  crear un  sitio web Wordpress implementado a travez de Docker (2 capas), donde a partir de dockerfiles, que se gestiona a travez de consola OrbStack (docker engine), levantando el sitio web en localhost. Posteriormente, migrar el contenido de mi sitio Web profesional, existente en AWS Cloud.
Adicional, explorando el apoyo del Asistente de Inteligencia Artificial Amazon Q.

### Application Architecture:
El sitio web se compone de (2) capas:  BASES DE DATOS - FRONTEND

**CONTENEDORES:**
- 1. BASE DE DATOS: MYSQL 
	Contenedor: database   puerto:3306 (puerto interno contenedor)
- 2. FRONTEND:  PHP - APP Legacy WORDPRESS
	Contenedor: wordpress     puerto:80 (puerto interno contenedor)

**PRE-REQUSITOS:**
En un ambiente local (tu maquina) necesitas;

-   Motor de DOCKER (engine docker) para crear esta solución de contenedores, entonces se puedes instalar y usar Docker Desktop o OrbStack  

-   Preferiblemente,  usar una Interfaz (IDE) de desarrollo como VSCODE. Si no puede usar tu consola o terminal para interactuar con la shell (linea de comando)

-   Crear un carpeta para tu proyecto y abrir o relacionar como workspace en VSCODE, con una subcarpeta para wordpress y otra para base de datos. Posterior, crearemos los demás archivos 

Exiten, dos formas de implementar:

**A. Implementando cada componente, por aparte**

-   Crear la RED en ambiente docker, que va a relacionar los (2) contenedores
```
> docker network create mysitewp-network
> docker network list
```
-   Crear archivo dockerfile BASE DE DATOS(MYSQL) y FRONTEND (PHP -WORDPRESS), por aparte


-   Crear la imagen  a partir del archivo dockerfile para cada uno. Se ubica en cada directorio del proyecto 
```
# para BASE DATOS
docker build -t ima-mysite-db .  
# para APP WORDPRESS   
> docker build -t ima-mysite-wp .    
```

#Crear o correr (RUN) los contenedores con todos los parámetros
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


**B.  Implementando a partir de la ejecución de un archivo docker-compose.yaml** 

En este caso, **se omite este punto. No se necesita crear RED de docker**. En este caso, se realiza a travez de archivo docker-compose.yaml

-   Crear archivo dockerfile BASE DE DATOS(MYSQL) y FRONTEND (PHP -WORDPRESS), por aparte
-   Crear el archivo docker-compose.yaml, el cual define la relacion y la dependencia de los contenedores entre si, para implementar los contenedores 

###  Implementation

**A. Implementando cada componente, por aparte**

Crear la RED en Docker. Este punto solo aplica al punto A. Implementando cada componente, por aparte
```
docker network create mysitewp-network
docker network list
```
#Crear imágenes, se ubica en cada directorio del proyecto 
```
# para BASE DATOS
docker build -t ima-mysite-db .  
# para APP WORDPRESS   
> docker build -t ima-mysite-wp .    
```
**B.  Implementando a partir de la ejecución de un archivo docker-compose.yaml**

*1. Crear los dockerfiles*

-   BASE DE DATOS(MYSQL):  En la sub carpeta Database, crear un archivo para dockerfile, el cual tiene los siguientes comandos y comentarios

-   FRONTEND (PHP -WORDPRESS):  En la sub carpeta wordpress, crear un archivo para dockerfile, el cual tiene los siguientes comandos y comentarios

*2. Crear el archivo DOCKER-COMPOSE (docker-compose.ymal)*, el cual define la relación y la dependencia de los contenedores entre si, para implementarlos

#Ejecutar solo docker-compose, que se basa en lo dockerfile creados anteriormente para Bases de datos/aplicación wordpres
```
> docker-compose up -d
```
-   Se agrega en este proyecto los archivos entrypoint.sh y php.ini para configurar las variables de entorno a nivel de Wordpress que permiten ampliar la configuración de PHP, con el fin de usar el plug-in de wordpress llamado ALL-in-One WP Migration (instalar el modulo en wordpress) 

-   El plug-in ALL-in-One WP Migration permite restablecer o importar un archivo tipo backup de sitio wordpress (ejemplo: mysite-cloud.wpress)  y migrar el contenido a esta nueva infraestructura local implementada con Docker. 


Finalmente, se implementa el docker-compose.ymal con comando docker-compose up -d

Para navegarlo a través de localhost puerto 8080
```
http://localhost:8080/
```

*3. Migracion Contenido Sitio WP*, anterior 

Una vez, activo el sitio http://localhost:8080/, se configura con usuario y password administrador de worpress.

-   Se instala el plug-in x, y se va opción import para cargar el archivo mysite-cloud.wpress (previo backup o exportación del sitio anterior wordpresss en cloud)

-   Se carga archivo mysite-cloud.wpress, donde se comprueba que la variables de entorno y para ampliar configuración de PHP (archivos entrypoint y php.ini), se ejecutaron y serán persistentes en el despliegue de los contenedores docker de esta solución.

Quedando migrado el sitio deplegado en contenedores en este caso, en ambiente localhost 

**Adicionales:**

* Gestion en consola OrbStack (engine de docker):*

-   Se observa los CONTENEDORES con su información
-   Se observa los VOLUMENES creados, con opción para exportar la data
-   Se observa las IMAGENES creadas de los contenedores

En consola OrbStack, permite acceder y manipular todos los artefactos (contenedores, imágenes, volúmenes),  creados 

* Uso y apoyo con Inteligencia Artificial mediante Amazon Q - modelo IA, Claude Sonnet 4.*. De uso a través de VSCODE.

### Technical Desing:

![Technical Desing](https://ocvpprofessional.cloud/wp-content/uploads/2025/11/WP-docker-Orbstack-AwsQ.png)
