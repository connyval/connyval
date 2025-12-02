## WordPress Website deployed by IAC-Terraform

### Objetive:
El Objetivo de este laboratorio, busca crear e implementar un nuevo sitio web Wordpress a travez de IAC (Infraestructura como Codigo) mediante TERRAFORM. Creando contenedores en EC2 para Wordpress.  Posteriormente, migrando contenidos de un sitio WP, anteiror.

Este proyecto Terraform es desarrollo en ambiente local para despliegue hacia AWS Cloud.

### Application Architecture:
El nuevo sitio web, se despliega mediante IAC con la siguiente arquitectura, construida en modulos Terraform:

- (1) VPC
- (2) subredes publicas en (2) zonas de disponibilidad
- (1) EC2 (Host), creación dinamica de los contenedores mediante Docker-compose.yml, para:
    - BASE DE DATOS: MYSQL (wordpress_db)
      Contenedor: database   puerto:3306 (puerto interno contenedor)
    - FRONTEND:  PHP - APP WORDPRESS (wordpress_app)
      Contenedor: wordpress puerto:80 (puerto interno contenedor)
- (2) Security Groups para EC2-ALB
- (1) Load Balancer (ALB-Application Load Balancer)
- (1) Route53 - registro tipo A para el dominio personalizado
- (1) Cloudfront - Creación distribución para ofrecer cache a la aplicación
- (1) ACM - registro de certificado SSL para dominio personalizado
- Opcional: Migración contenidos wordpress mediante importación (uso archivo backup tipo .wpress) de sitio web, anteiror.

**PRE-REQUISITOS:**
- Acceso a cuenta AWS (ambiente de trabajo) o cuenta AWS(Sandbox)
- Instalada y cónfigurada AWS CLI (Comand line AWS)
- Instalación y configuración extensiones TERRAFORM en VSCode
- Crear una carpeta para proyecto (local) y relacionar como workspace en VSCODE.
- IDE como Visual Studio Code incluye Asistente Amazon Q (Asistente IA) 
- Opcional: disponer de backup sitio wordpress, anterior (archivo tipo .wpress) 

**Creación codigo proyecto TERRAFORM**

1. **Modulos:** De acuerdo a mejores practicas, se crea el proyecto organizado y estructurado en modulos. En cada modulo se estructura en (3) archivos: **main.tf, outputs.tf y variables.tf**, asi:

```
> docker network create mysitewp-network
> docker network list
```

- **Modulo VPC:** 
    - En **submodulo main.tf**
        - Se crea la VPC "Main" definiendo CIDR de red
        - A nivel de zonas de disponbilidad, se usa  Data source (información del provieder) para buscar la disponbilidad, de las mismas
        - se crea (2) Subredes publicas, definiendo CIDR, correspondiente
        - se crea (1) Tabla de Enrutammiento publico y se asocia a la subredes publicas
        - se crea (1) Internet Gateway y se atacha a la VPC
    - En **submodulo outputs.tf**, se define las salidas de este modulo
    - En **submodulo variables.tf**, se define las varibles de este modulo

- **Modulo EC2:**
    - En **submodulo main.tf**
        - Se crea "sts:AssumeRole" para dar acceso seguro y proporcionar Admministración de la EC2 por session Manager
        - Se crea el Security Group para EC2, definiendo reglas de entrada (puerto 80/22) y salida (puerto 80)
        - Se crea el Security Group para ALB, definiendo reglas de entrada y salida
         * se crea la instancia EC2, definiendo AMI (en base a archivo userdata.sh), tipo instancia, key pair (antes definida), asociacion a VPC y subredes publicas, asociacion al Security Group de EC2
        - Se instala Docker y Docker-compose en la instancia EC2 (definido en archivo userdata.sh), dinamicamente
        - En archivo docker-compose.yml (definido en archivo userdata.sh), se crea y configura los contenedores para MYSQL (wordpress_db) y WORDPRESS (wordpress_app), sus variables de entorno, volumenes y redes
        - Se agrega variables y configuración para el archivo wp-config.php y php.ini para aumentar capacidad de carga de archivos en la aplicacion wordpress
        - Se inicia los contenedores docker (definido en archivo userdata.sh)
    - En **submodulo outputs.tf**, se define las salidas de este modulo
    - En **submodulo variables.tf**, se define las varibles de este modulo

- **Modulo ABL:**
    - En **submodulo main.tf**
        - Se crea security group para el Balanceador de aplicacion
        - Se crea (2) reglas de entrada para puerto 80 y 443
        - Se crea (1) regla de salida para internet 
        - Se crea Target Group apuntando a la EC2, creada 
        - Se crea un healthcheck para validar trafico HTTP
        - Se crea (2)listerner, uno para puerto 80 redireccionado (forwarding) a 443. El otro listener para puerto 443, como tal en protocolo HTTPS y aplicando el ARN (nombre de recurso AWS) del certficado SSL (creado previamente en ACM)
        - Se atacha el Target Group al Balanceador de aplicación
    - En **submodulo outputs.tf**, se define las salidas de este modulo
    - En **submodulo variables.tf**, se define las varibles de este modulo

- **Modulo CLOUDFRONT:**
    - En **submodulo main.tf**
        - Se crea la distribución Cloudfront, definiendo el origen como el ALB, creado
        - Se define el comportamiento (behaviors) y por defecto para el cache. Se define el viewer protocol policy como redireccion HTTP a HTTPS
        - Se selecciona el certificado SSL para dominio personalizado asignando ARN (nombre de recurso AWS) del certficado SSL (creado previamente en ACM)
    - En **submodulo outputs.tf**, se define las salidas de este modulo
    - En **submodulo variables.tf**, se define las varibles de este modul

- **Modulo ROUTE53:** 
    - En **submodulo main.tf**
        - Se crea el registro tipo A, definiendo el nombre de dominio personalizado, donde se relaciona a la ditribucion cloudfront, creada.
    - En **submodulo outputs.tf**, se define las salidas de este modulo
    - En **submodulo variables.tf**, se define las varibles de este modul

2. **A nivel global del proyecto**, se crea los archivos **main.tf, outputs.tf, variables.tf**, en los cuales se invocan los modulos para el depliegue de la infraestructura en AWS.

- En **submodulo main.tf**
    - se invoca el modulo VPC
    - se invoca el modulo EC2
    - se invoca el modulo ALB
    - se invoca el modulo CLOUDFRONT
    - se invoca el modulo ROUTE53
- En **submodulo outputs.tf**, se define las salidas globales del sitio web
- En **submodulo variables.tf**, se define las varibles globales de alcancen modulos 

**NOTAS:**
- Se debe configurar el archivo **provider.tf** para definir el proveedor AWS y la región de trabajo

- Previamente en ROUTE53, se habia creado la zona HOST del dominio personalizado, donde genero los registros iniciales de NS (Name Server de AWS) de la zona. Para lo cual, posteriormente, en la pagina o configuracon del proveedor externo (proveedor donde se adquirio el dominio) se actuzaliza los NS dados por AWS, con el fin de desplegar el sitio web final hacia Internet. 

- En ACM, se registro previamente el certificado de servidor seguro SSL para dominio personlaizado. Es decir, se registro del certificado SSL usado en Balanceador y  Distribución en CLOUDFRONT

- De sitio web anterior, desde aplicacion wordpress, se genera o crea el backup **(archivo mysite-cloud.wpress)**, el cual se usa para migrar contenidos al nuevo sitio WP.

### Final Implementation:

**A. Despliegue final infraestructa AWS mediante TERRAFORM**

- Inicialmente, se ejecuta **terraform init**
- Se verifica formato **terraform frm**
- Se valida la configuración **terraform validate**
- Se crea el plan de despliegue **terraform plan**. Se itera varias veces hasta lograr el despligue y  configuracion final, deseada
- Se aplica el plan de despliegue **terraform apply**
- Se verifica en coósola AWS, la creación de los recursos y su correcta configuración
- Una vez, deplegada la infraestructura, se navega al sitio personalizado con protocolo seguro https://misitio.cloud, el cual fue instalado con un wordpress limpio (nueva instalacion wordpres)

**B. Migración contenido WordPress al nuevo sitio**

- Se loguea a nuevo sitio wordpress https://misitio.cloud/wp-admin.
- Se va al menu principal WP y seleciona menu PLUGINS (opcion ADD-PLUGINS), se instala y activa el modulo o plug-in **ALL-in-One WP Migration**, que permite importar un archivo tipo backup (mysite-cloud.wpress)  y migrar el contenido del sitio anterior al nuevo sitio desplegado. 

Finalmente, se *implementa el nuevo sitio web Wordpress migrado bajo infraestructura desplegada IAC mediante TERRAFORM, exitosamente!!.

**Adicional:**
**Uso y apoyo de Amazon Q (asistente IA) integrado a VScode** para apoyo en consulta y corrección codificacion para el proyecto TERRAFORM.

### Technical Desing:

![Technical Desing](https://ocvpprofessional.cloud/wp-content/uploads/2025/11/WP-docker-Orbstack-AwsQ.png)
