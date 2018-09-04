# Docker Swarm

Un Swarm consiste en varios hosts Docker que se ejecutan en Docker Swarm y actúan como managers y workers.

## Conceptos llave

**Nodes**

Un nodo en es una instancia de Docker que participa en el Swarm.

Manager: Son los encargados de gestionar el clúster. Entre todos los Manager se elige automáticamente un líder y éste es el encargado de mantener el estado del clúster. También son los encargados de distribuir las tareas entre los workers.

Worker: Reciben las tareas de los Manager y las ejecutan.

**Services and tasks / Servicios y tareas**

Es la definición de las tareas para ejecutar en los nodos de manager o workers.

Cuando creas un servicio, especificas una imagen del contenedor a usar y los comandos que se ejecutaran cuando el contendor este corriendo.

Replicado: Swarm creara una tarea por cada replica que le indiquemos para después distribuirlas en el clúster.

**Global:** Swarm ejecutara una tarra en cada uno de los nodos del clúster.

**Task:** Suma de un contenedor más el comando que ejecutamos dentro de el mismo.

Ante la caída de una tarea, Swarm es capaz de crear otra similar en ese u otro nodo para cumplir con el número de réplicas definido.

Todos los nodos del clúster enrutarán a una tarea que esté ejecutando el servicio solicitado.

Swarm cuenta con un DNS interno que asigna automáticamente una entrada a cada uno de los servicios desplegados en el clúster.

**Load balancing**

Los managers usan el sistema de balanceo para exponer los servicios que se desea exponer externamente a Swarm.

## Creación de clúster

-   Partimos de un nodo destinado a ser Manager

    -   **docker swarm init --advertise-addr \[IP\]**

    -   Iniciamos ese nodo como Manager

    -   El parametro *–advertise-addr* es obligatorio e indicamos la IP del Manager y cuyo puerto por defecto es el *2377*

    -   La salida del comando nos muestra dos tokens. Cada uno de ellos sirve para unir nodos Manager y Worker adicionales.

-   Añadir un nodo Worker al clúster (Desde worker)

    -   **docker swarm join –token \[token\] \[IP:puerto\]**

    -   Utilizamos el token para nodos Worker e indicamos la IP y el Puerto de uno de los nodos manager.

## Gestión de servicios

-   Con el clúster preparado:

-   Damos de alta a un nuevo servicio desde la consola del manager:

    -   **docker service create --replicas 1 --name helloworld alpine ping enmilocalfunciona.io**

    -   –name es el nombre del servicio

    -   –replicas es el número de tareas que vamos a crear

-   Ver el listado de clúster:

    -   **docker service ls**

-   Ver información del servicio

    -   **docker service inspect –pretty helloworld**

-   Ver todas las tareas que se están ejecutando para el servicio

    -   **docker service ps helloworld**

-   Aumentar o disminuir el número de replicas

    -   **docker service scale helloworld=5**

-   Listado de contenedores de un servicio

    -   **docker stack ps \[servicio\]**

-   Detener un servicio

    -   **sudo docker stack rm \[servicio\]**

-   Lanzar un servicio

    -   **docker stack deploy -c docker-compose.yml \[servicio\]**

-   Para crear volúmenes persistentes la ruta debe existir en cada nodo de enjambre

-   Limpiar docker swarm

    -   **Docker swarm leave --force**

## Lanzar un servicio con docker-compose.yml
-   Con el alrchivo *docker-compose.yml* creado lanzamos el servicio

> docker stack deploy -c docker-compose.yml *[nombre del servicio]*

-   Ver listado de servicios con las replicas
  
> docker service ls

## Rolling Updates

-   La capacidad de gestionar actualizaciones en los servicios

-   Las actualizaciones se pueden hacer secuencialmente o en paralelo

-   **docker service create --replicas 4 --name redis --update-delay 10s --update-parallelism 2 redis:3.0.6**

    -   –update-delay es el tiempo que esperará hasta empezar la actualización de la siguiente tarea

    -   –update-parallelism será el número de tareas que actualizará en paralelo

-   Para realizar una actualización le indicamos una imagen

    -   docker service update --image redis:3.0.7 redis

# Bibliografía

[<span class="underline">http://enmilocalfunciona.io/cluster-de-docker-con-swarm-mode/</span>](http://enmilocalfunciona.io/cluster-de-docker-con-swarm-mode/)
