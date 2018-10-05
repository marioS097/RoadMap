# Objetivos Kubernetes

## Conocimiento

- Arquitectura
- Tipos de Apps --> Bestfferd...
- Kubectl (CLI)
- Ficheros YAML / JSON
  - Deployments --> ReplicaController / ReplicaSet
  - Services
  - Pods
  - Ingress Controller

## Ejercicio

- Instalación --> 1 Master y 2 Workers
  - Diferencias entre 1 master y varios master
  - Almacenamiento (NFS) Network File System
  - Acceder a Apps (Varias opciones)
    - Ingress Controller
    - ¿...?

---

## Aquitectura

- El clúster de contenedores consta de al menos un clúster maestro y varias máquinas trabajadoras llamadas nodos o minions
- Los componentes del *Master* y los componentes de los *Nodos* y cómo se comunican a través de la API y Kubelet:

|MASTER|NODOS|
|:-:|:-:|
|SCHEDULER|KUBE-PROXY|
|KUBE-CONTROLLER|PODSDOCKER|
|KUBE-APISERVER|CADVISOR|
|ETCD|KUBELET|

### Clúster Maestro

- Ejecuta los procesos del plano de control de Kubernetes
- Administra el ciclo de vida del maestro cuando crea o elimina un clúster.
- **El maestro del clúster y la API de kubernetes**
  - El maestro es el punto de conexión unificado del clúster. Todas las interacciones con el clúster se realizan a través de llamadas a la API de Kubernetes.
  - Se puede realizar la llamada a la API de Kubernetes directamente (mediante HTTP) o indirectamente mediante CLI, ejecución desde la linea de comandos, (*kubectl*).
  - El proceso del servidor API del *master* es el centro de todas las comunicaciones para el clúster. Todos los procesos internios del clúster (nodos, el sistema y los componenetes, los controladores de aplicación) actúna como clientes del servidor API.
- **Interacción entre el maestro y el nodo**
  - El maestro se ocupa de decidir qué se ejecuta en todos los nodos del clúster. Incluye:
    - Programación de cargas de trabajo (Alpicaciones en contenedores)
    - Actualización de las cargas
    - Gestión del ciclo de vida
    - Escalado
    - Administracíon de los recuros de red y almacenamiento
  - El maestro y los nodos se comunican usando la API de Kubernetes
- **Servicios**
  - **API Server (kube-apiserver):** Provee la API que controla la orquestación de Kubernetes, y es el responsable de mantenerla accesible. El *apiserver* expone una interfaz REST que procesa operaciones cómo la creación/configuración de *pods* y servicios, actualización de los datos almacenados en *etcd*. También es el responsable de que los datos de *etcd* y las características de los servicios de los contenedores deplegados sean coherentes. Permite que distintas herramientas y librerías puedan comunicarse de manera más fácil.
  - **etcd:** Es una base de datos que almacena *claves-valor* en el que Kubernetes alamcena información (configuración y metadatos) acerca de él mismo (pods, servicios, redes...) para poder ser utilizada por cualquier nodo del clúster. También se almacena el estado del clúster.
  - **Scheduler (Kube-scheduler):** Se encarga de distribuir los *pods* entre los *nodos*. Lee los requisitos del *pod*, analiza el clúster y selecciona los nodos aceptables. Se comunica con el *apiserver* en busca de pods no desplegados para desplegarlos en el nodo que mejor satisface los requerimentos. También es responsable monitorizar la utilización de recursos de cada host para asegurar que los pods no sobrepasen los recursos disponibles.
  - **Controller manager:** Es un servicio usado para manejar el proceso de replicación definido en las tareas de replicación. Los detalles de estas opciones son descritas en el *etcd*, dónde el *controller manager* observa los cambios. Cuando un cambio es detectado, el *controller manager* lee la nueva información y ejecuta el proceso de replicación hasta alcanzar el estado deseado.
  
### Nodos

- Un clúster de contenedor generalmente tiene uno o más nodos, que son las máquinas de trabajador que ejecutan las aplicaciones en contenedor y otras cargas de trabajo.
- Son máquinas físicas o virtuales que ejecutan Docker
- Las máquinas individuales son *instancias en VM de Compute Engine* que Kubernetes Engine crea en tu nombre cuando creas un clúster
- Cada nodo se gestiona desde el maestro (recibe actualizaciones sobre el estado de cada nodo)
- Posibilidad de ejercer un control manual sobre el ciclo de vida del nodo o dejar que Kubernetes Engine realice reparaciones y actualizaciones automáticas
- **Servicios**
  - Un nodo ejecuta los servicios necesarios para admitir los contenedores Docker que conforman las cargas de trabajo de tu clúster
  - Como el tiempo de ejecución Docker
  - Y el agente de nodo Kubernetes (*kubectl*). Se comunica con el maestro y se encarga de iniciar y ejecutar los contenedores Docker programados en ese nodo.
  - **Docker:** Motor de contenedor que funciona en cada nodo descargando y corriendo las imágenes docker. Son controlados a través de su API por *kueblet*
  - **Kubelet:** Gestiona los *pods* y sus contenedores, imágenes, volumenes, etc. Cada nodo corre un kubelet, que es el responsable del del registro de cada nodo y de la gestión de los *pods* corriendo ese nodo. Kubelets pregunta a la *API server* por *pods* para ser creados y desplegados por el *Scheduler*. Tambié gestiona y comunica la utilización de recursos, el estado de los *nodos* y de los *pods* corriendo en el.
  - **cAdvisor:** Es un agente de uso de los recuros y análisis que descubre todos los contenedores en la máquina y recoge información sobre la CPU, memoria, sistemas de ficheros y estadísticas del uso de la red. Tasmbién proporciona  el uso general de la máquina medianre el análisi del contenedor raíz en la máquina
  - **Flannel:** provee redes y conectividad para los nodos y contenedores en el clúster. Utiliza *etcd* para almacenar sus datos.
  - **Proxy (Kube-proxy):** Provee servicios de proxy de red. Cada nodo también ejecuta un proxy y un balanceador de carga. Los servicios *endpoints* se encuentran disponibles a través de DNS o de variables de entorno de Docker y Kubernetes que son compatibles entre ambos. Estas variables se resuelven en los puertos administrados por el proxy.

---

## Conceptos base (YAML/JSON)

- **Deployments:** Un controlador de implementación proporciona actualizaciones declarativas para *Pods* y *ReplicaSets*.
  - Describe un estado deseado en un objeto de Despliegue(YAML/JSON), y el controlador de despliegue cambia del estado real al deseado a un velocidad controlada. Puee definir implementaciones para crear nuevos conjuntos de réplicas, o para eliminar implementaciones existentes y adoptar todos sus recusos con despliegues nuevos.
- **ReplicaSet:** Garantiza que se ejecute un número especifico de réplicas de pod en cualquier momento, es decir que esten siempre activos y disponibles.
- **Service:** Es una abstracción (un nombre único) que define un conjunto de *pods* y la lógica para acceder a los mimos. Normalmente un conjunto de *Pods* están destinados a un servicio. El conjunto de *Pods* que está etiquetado por un servicio está determinado por un "Selector de Labels" (también podríamos querer tener un servicio sin un Selector).
  - Con esto se consigue que si hay un cambio en un *Pod*, esto es totalmente transparente para el usuario y el servicio.
  - Los servicios ofrecen la capacidad para buscar y distribuir el tráfico porporcionando un nombre y dirección o puerto persistente para los *pods* con un conjunto de *labels*
  - Para aplicaciones nativas en Kubernetes, Kubernete ofrece una API de puntos finales qye se actualiza cada vez que un conjunto de *Pods* en un servicio cambia. Para aplicaciones no nativas Kubernetes ofrece un pente basado en IP's virtuales para servicios que redirige a *Pods back-end*
- **PODs (Docker):** Son la unidad más pequeña desplegable que puede ser creada, programada y manejada por Kubernetes. Son un grupo de contenedores *Dockers*, de aplicaciones que comparten volúmenes y red. Son las unidades más pequeñas que pueden ser creadas, programadas y manejadas por Kubernetes. Deben ser desplegados y gestionados por controladores de réplicas.
  - **Pods que corren en un solo contenedor:** Son los más comúnes. En estos casos se puede pensar en un *Pod* como si fuera un envoltorio alrededor de un solo contenedor, en este caso Kubernetes adminisrta los *Pods* en ligar de los contenedores directamente.
  - **Pods que corren en varios contenedores y necesitan trabajar juntos:** Un *Pod* puede encapsular una aplicación compuesta por múltiples contenedores ubicados conjuntamente que están estrechamente conectados y necesitan compartir recursos. El *Pod* agrupa estos contenedores y recursos de almacenamiento juntos como una única entidad manejable
- **Ingress controllers:** Los servicios y pods tienen IPs solo enrutables por la red del clúster. Todo el tráfico que termina en un enrutador de borde se descarta o se reenvía a otro lugar. Los *Ingress* son una colección de reglas que permiten que las conexiones entrantes lleguen a los servicios del clúster.
- **Clúster:** Conjunto de máquinas físicas o virtuales y otros recuros (almacenamiento, red, etc.) utilizados por Kubernetes dónde *pods* son desplegados, gestionados y replicados. Los clústeres de nodos están conectados por red y cada *nodo* y *pod* pueden comunicarse entre si. El tamaño de un clúster Kubernetes está entre 1 y 1000 *nodos*.
- **Replication controller:** Se asegura de que el número especificado de réplicas del *pod* estén ejecutándose y funcionando en todo momento. Manejan el ciclo de vida de los *pods*, creándolos y destruyendolos como sea requerido. Permite escalar de forma fácil los sitemas y maneja la recreación de un *Pod* cuando ocurre un fallo. Gestiona los *Pods* a través de labels.
- **Labels:** Son pares de clave/valor usados para agrupar, organizar y seleccionar un grupo de objetos tales como *Pods*. Son fundamentales para que los *services* y los *replications controllers* obtengan la lista de los servidores a donde el tráfico debe pasar.