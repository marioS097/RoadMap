# Docker

Permite meter en un contenedor todas aquellas cosas que mi aplicación necesite para ser ejecutada, junto con la propia aplicación.

Docker tiene dos partes, un demonio que debe arrancarse de fondo, y el cliente Docker que usamos para trabajar con él en consola.

## Contenidos/Conceptos a tener en cuenta

### Contenedor:

Los contenedores de aplicaciones son entornos ligeros de tiempo de ejecución que proporcionan a las aplicaciones los archivos, las variables y las bibliotecas que necesitan para ejecutarse, maximizando de esta forma su portabilidad.

### Gestión de imágenes:

### Configuración NetWork:

### Volúmenes:

### Dockerfile: 

### Docker Images

Son plantillas de sólo lectura que contienen el sistema operativo base dónde correrá nuestra aplicación, además de las dependencias y software adicional instalado, necesario para que la aplicación funcione correctamente. Las plantillas son usadas por Docker Engine para crear los contenedores Docker.

### Docker Containers

El contenedor de Docker aloja todo lo necesario para ejecutar un servicio o aplicación. Cada contenedor es creado de una imagen base y es una plataforma aislada. Un contenedor es simplemente un proceso para el sistema operativo, que se aprovecha de él para ejecutar una aplicación. Dicha aplicación sólo tiene visibilidad sobre el sistema de ficheros virtual del contenedor.

## Instalar un contenedor

1.  Ver las imágenes preconfiguradas

> **$ docker search centos**

2.  Instalar una versión Ubuntu 14.04

> **$ docker pull ubuntu:14.04**

3.  Visualizar las imágenes de nuestro anfitrión

> **$ docker images**

4.  Acceder al contenedor además de crearlo. Hay 2 formas

4.1.  Mediante IMAGE ID

> **$ docker run -i -t b72889fa879c /bin/bash**

4.2.  Mediante REPOSITORY y TAG

> **$ docker run -i -t ubuntu:14.04 /bin/bash**

5.  Añadir etiqueta personalizada

> **$ docker tag b72889fa879c oldlts:latest**

## Comandos básicos en Docker

-   Descargar una imagen:

**docker pull** *\[NombreImagen\]*

-   Borrar imágenes y contenedores

**docker stop** $(docker ps -a -q)
-   Detiene imagenes

**docker rm** $(docker ps -a -q)

-   Muestra las imágenes descargadas

**docker images**

-   Muestra los contenedores en funcionamiento

**docker ps**

-   Muestra la información de las imágenes

**docker info**

-   Buscar dentro de Docker

**Docker search *\[Name\]***

-   Información acerca del contenedor

**docker inspect** *\[\<friendly-name\|container-id\]*

-   Borrar todas las imagenes y contenedores

**docker system prune –af**

-   Entrar en un contenedor

**docker exec -it** *\[container-id\]* **bash**

## Dockerfile

-   **FROM** Especifica la imagen base que se va a usar en el Dockerfile

-   **MAINTAINER** Especifica en autor

-   **RUN** Ejecuta en comando para construir la imagen

-   **ENV** Establece las variables de entorno

-   **CMD** Ejecuta los comandos al iniciar el contenedor

-   **EXPOSE** Expone el puerto especificado a la maquina host

## Errores

-   Error al ejecutar Spring en clustres *Mapping filter: 'requestContextFilter' to: \[/\*\]*

    -   Añadir al Dockerfile

    -   \["java", **"-Djava.security.egd=file:/dev/./urandom",** "-jar", "/usr/src/app/mongodb.war"\]

## Docker Network

-   Crear la Network

**docker network create \[nombreNetwork\]**

-   Añadir el contenedor a a la Network

**docker run --name=\[nombreContainer\] --net=\[nombreNetwork\]**

-   Hacer ping

**docker run --net=\[nombreNetwork\] \[contenedor actual\] ping -c\[numero\] \[contenedor objetivo\]**

-   Conectar contendores existentes a la red

**docker network connect \[nombreNetwkork\] \[contenedor\]**

-   Crear alias en las Network

**docker network connect --alias \[alias\] \[nombreNetwork\] \[contenedor\]**

-   Listar Network

**docker network ls**

-   Datos de una network

**docker network inspect \[network\]**

-   Desconectar un contenedor de la network

**docker network disconnect \[nombreNetwork\] \[contenedor\]**

## Volúmenes

-   Crear volumen persistente

**docker run -v \[rutaHost\]:\[rutaContenedor\] **

-   Mapea los volúmenes asignados desde el contenedor fuente al contenedor que se está lanzando.

**docker run --volumes-from \[contenedorFuente\] -it \[contenedorDestino\]**

## COPY vs ADD

**COPY** Coge el *src* y el destino. Copia un archivo a directorio local desde el host

**ADD** Hace lo mismo que el COPY pero agrega dos funcionalidades.

1.  Poder utilizar una URL en lugar de un archivo / directorio local

2.  Poder extraer un archivo *tar* desde la fuente en el destino

En la mayoría de de los casos primero se descarga el *zip* y luego se descomprime en el local dentro de un comando *RUN*

Se suele usar **COPY** porque es más específico

## CMD vs ENTRYPOINT

**CMD** Establecer un comando predeterminado (Mientras no se ejecute el contenedor con otro comando) En el Dockerfile solo se ejecutará el ultimo **CMD**

**ENTRYPOINT** Permite configurar un contenedor que correrá como ejecutable.

1.  **Exec form** (ENTRYPOINT \[“exec”, “param1”, “param2”\]) Ejecuta comandos y parámetros que pueden utilizar el **CMD** para ampliarlos

2.  **Shell form** (ENTRYPOINT command param1 param2) Ignora los comandos de linea que se ejecutan por Docker o CMD

## Docker Compose

-   Docker Compose permite definir nuestra aplicación multicontenedor en un archivo con las mismas propiedades que indicaríamos con el comando docker run individualmente

-   **ERROR**: Couldn't connect to Docker daemon at http+docker://localhost - is it running

    -   export DOCKER\_HOST=127.0.0.1

### Comandos Basicos

-   docker-compose version

-   docker-compose build

-   docker-compose up

-   docker-composer ps

-   docker-compose stop

-   netstat -ntl \| grep 8080

# Bibliografía

[<span class="underline">http://informatica.gonzalonazareno.org/proyectos/2015-16/proyectoDK.pdf</span>](http://informatica.gonzalonazareno.org/proyectos/2015-16/proyectoDK.pdf) (Docker-engine, Ubuntu Trusty 14.04 (LTS))

[<span class="underline">https://www.danielcastanera.com/comandos-utiles-en-docker/</span>](https://www.danielcastanera.com/comandos-utiles-en-docker/) (Comandos útiles en Docker)
