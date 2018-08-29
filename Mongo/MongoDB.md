# Mongo

Es un sistema de base de datos NoSQL orientado a documentos, desarrollado bajo el concepto de código abierto.

Guarda estructuras de datos en documentos similares a **JSON** con un esquema dinámico

## Instalación

### Agregar el Repositorio MongoDB

1.  Importar la clave oficial del repositorio de MongoDB

> **sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927**

1.  Una vez importada la clave, creamos una lista de carpetas de MongoDB

> **echo "deb http://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.2 multiverse" \| sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list**
>
> *El comando* **tee** *permite copiar la entrada estándar de un comando a un archivo y así mismo seguir teniendo salida estándar por pantalla/terminal.*

1.  Después de agregar los detalles del repositorio, debemos actualizar la lista de paquetes.

> **sudo apt-get update**

### Instalando y Verificando MongoDB

1.  Instalar el propio paquete MongoDB

> **sudo apt-get install -y mongodb-org**

1.  Crear un archivo de unidad para administrar el servicio de MongoDB

> **sudo nano /etc/systemd/system/mongodb.service**

1.  Editarlo y añadir:

> *\[Unit\]*
>
> *Description=High-performance, schema-free document-oriented database*
>
> *After=network.target*
>
> *\[Service\]*
>
> *User=mongodb*
>
> *ExecStart=/usr/bin/mongod --quiet --config /etc/mongod.conf*
>
> *\[Install\]*
>
> *WantedBy=multi-user.target*

1.  **Unit:** Contiene un resumen, así como las dependencias que deberán existir antes de que el servicio inicie. En nuestro caso, MongoDB depende de que la red esté disponible, por lo tango agregamos *network.tarket* aquí.

2.  **Service:** Indica como deberá iniciar el servicio. La directiva User especifica que el servicio deberá correr bajo el usuario mongodb, y la directiva ExecStart inicia el comando para arrancar el servidor MongoDB.

3.  **Install:** Le dice a systemd cuando el servicio debe iniciar automáticamente. multi-user.target es un sistema de secuencias de arranque estándar, que significa que el servicio correrá automáticamente al arrancar.

<!-- -->

1.  Editar el fichero de configuración para aceptar todas las IP

> **sudo nano /etc/mongod.conf**
>
> Y editamos la línea
>
> *bindIp: 127.0.0.1* *bindIpAll: true*

1.  Iniciar el servicio creado con *systemctl*

> **sudo systemctl start mongodb**

1.  Para revisar el servicio arrancado

> **sudo systemctl status mongodb**

1.  Que se inicie el servicio automáticamente cuando el sistema se inicia

> **sudo systemctl enable mongod**

1.  Parar el servicio creado con *systemctl*

> **sudo systemctl stop mongodb **

1.  Reiniciamos el servicio

> **sudo systemctl restart mongodb**

## Ejemplos de comandos:

*Los comentarios se hacen añadiendo **//** al principio de la línea*

### Creando/Usando una base de datos:

Este comando creara la base de datos “pruebas” y nos moverá hacia ella. Si la base de datos ya existe, tan solo nos mueve hacia ella para poder usarla, en caso de no existir la base de dato, el sistema la crea.

**\> use pruebas**

**switched to db pruebas**

### Listando las bases de datos:

Si queremos obtener un listado de las bases de datos que están en nuestra instancia MongoDB tan solo tecleamos:

**\> show dbs**

**admin**

**local**

**pruebas**

**test**

### Creando Colecciones e insertando documentos:

MongoDB es una base de datos del tipo documentos en la que almacena dichos documentos en una especie de agrupaciones conocidas como colecciones, estas colecciones vendrian siendo algo asi como las tablas en MySQL.

**db.prueba01.insert({titulo: "Primera prueba", contenido: "Mi primera prueba de insercion de un documento"})**

1.  **prueba01** es el nombre de la colección donde se encuentra el documento.

2.  **{titulo: “Primera prueba”, contenido: “Mi primera prueba de insercion de un documento”} **es el documento que estamos insertando en la colección.

### Listando las colecciones en una base de datos:

Para obtener un listado de las colecciones almacenadas en la base de datos en la que nos encontramos podemos teclear:

**\>show collections**

**perros**

**prueba01**

### Mostrar los documentos almacenados en una colección:

Para ver todos los documentos almacenados en nuestra coleccion **prueba01** podemos usar el siguiente comando:

**db.prueba01.find()**

### Gestión de usuarios:

-   Lo primero será crear un usuario con derechos de administrador, para ello accedemos a admin

**\>use admin**

**\>db.createUser( **

**{user: “nombreUsuarioAdmin”,**

**pwd: “contraseñaUsuarioAdmin”**

**roles:\[{role:”root”, db:”admin”}\]**

-   Abrir los puertos en el servidor Linux

**sudo iptables -A INPUT -p tcp --dport 27017 -j ACCEPT**

-   Para conectarse a la base de datos con estas credenciales

**$ mongo *\[--host 172.28.128.8 –port 27017\]* –u nombreUsuarioAdmin –p contraseñaUsuarioAdmin --authenticationDatabase admin**

### Comandos añadidos

Insertar una entrada

db.workers.insert(**{firstName: 'Carlos', lastNames: 'Garcia Perez', age: 36}**);

Realizar una consulta genérica

db.workers.find();

Consulta por firstName.

db.workers.find(**{firstName: 'Carlos'}**);

Por defecto las búsquedas son case sensitive, si queremos una búsqueda que no lo sea:

db.workers.find(**{firstName: /\^carlos$/i}**);

Inserción de entrada usando nuevo campo de tipo fecha / hora

db.workers.insert(**{firstName: 'Ivan', lastNames: 'Garcia Perez', age: 38, **

**created: new Date(2013, 2, 27)}**);

Entradas con más de 36 años.

db.workers.find({**age: {$gt: 36} }**);

Entradas que no tengan el campo created.

db.workers.find({**created: {$exists: false}}**);

Devuelve los resultados de forma más legible con saltos de linea y tabulaciones

db.workers.find(**{firstName: 'Carlos'}**)**.pretty()**;

Ordena de manera descendente los trabajadores basándose en el número de años de experiencia.

db.workers.find()**.sort({experienceYears: -1})**;

Los dos primeros empleados que sepan html a partir del segundo documento (paginacion)

db.workers.find**({tags: 'html'}**)**.limit(2).skip(1)**;

Elimina todas las entradas

db.workers.remove();

Contar

Db.workers.count();

Dame una entrada que tenga más de 5 años de experiencia laboral

db.workers.**findOne**( **{experienceYears: {$gt: 5} }** );

Especificando un tercer parámetro a true, actualizará el empleado si existe y sino lo creará.

Si queremos actualizaciones multiples necesitamos especificar el cuarto parámetro a true.

db.workers.**update**();

Creación de un índice (1 ascendente, -1 descendente)

db.workers.**ensureIndex**(**{"location.city": 1}**);

Eliminación de un índice

db.workers.**dropIndex**(**{"location.city": 1}**);

Muestra información sobre el índice que fue usado para ejecutar la búsqueda

db.workers.find({'location.city': 'nav'})**.explain()**;

Exportación a JSON

**mongoexport --db test -collection workers –fields** firstName,lastNames,experienceYears,location

Devuelve una lista con los métodos aplicables al objeto db

db.**help()**;

Deteniendo el servidor:

**use admin **

db.**shutdownServer()**;

## Bibliografía

[<span class="underline">https://www.digitalocean.com/community/tutorials/como-instalar-mongodb-en-ubuntu-16-04-es</span>](https://www.digitalocean.com/community/tutorials/como-instalar-mongodb-en-ubuntu-16-04-es) (Instalación de Mongo)

[<span class="underline">http://www.carlos-garcia.es/tutorial/mongodb-primeros-pasos</span>](http://www.carlos-garcia.es/tutorial/mongodb-primeros-pasos) (Comandos de Mongo)

[<span class="underline">https://blog.jam.net.ve/2011/01/09/usos-basicos-de-mongodb-console/</span>](https://blog.jam.net.ve/2011/01/09/usos-basicos-de-mongodb-console/) (comandos básicos de mongodb)

[<span class="underline">https://blog.jam.net.ve/2011/01/09/usos-basicos-de-mongodb-console/</span>](https://blog.jam.net.ve/2011/01/09/usos-basicos-de-mongodb-console/) (Nueva BD)
