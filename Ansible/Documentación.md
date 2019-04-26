# Documentación Ansible

## Ejercicio

- Conceptos básicos de automatización -> para qué, por qué, 
  - Inventario
  - Módulos
  - Playbooks
    - Etiquetas
    - Handlers
  - Roles
  - Files
  - Templates
  - Tasks
  - Handlers
  - (cualquier concepto que consideréis interesante y que se deba incluir)  
- Ejercicios -> En una arquitectura con 2 instancias -> 1 para ansible y otra para instalar cosas
  - Instalar Ansible, última versión
  - Entender todos los conceptos antes introducidos
  - 1 playbook -> Instalar un Apache en la otra instancia (Para estos 2, debéis buscar roles y/o playbooks ya existentes)
  - 1 playbook -> Instalar un MySQL en la otra instancia 
  - En otro playbook -> Cambiar la configuración del Apache con Ansible (por ejemplo, cambiar el index del Apache) -> HACEDLO VOSOTROS DESDE 0

- Todo lo que leáis / encontréis / aprendáis nos lo presentaréis el Miercoles día 17/04 (os mando convocatoria aparte) en una sesión de 15 minutos (cada uno) con una PPT.

- Como os hemos comentado, tenéis que hacerlo de forma individual para ver cómo avanzáis cada uno y para “simular” la entrada en un proyecto nuevo.

---
## Ansible

- **Ansible** es una plataforma de software libre para configurar y administrar computadoras.
- Es categorizado como una herramienta de orquestación
- Maneja nodos a través de SSH (Requiwre Python en los nodos)
- Está disponible para diferentes disribuciones Linux y para Mac
- En otros C.M. describes el estado deseado y el sistema lo configura. En Ansible se especifica cada paso a dar y así llegas al estado deseado.

- Arquitectura
  - Controlador: Máquina desde la que comienza la orquestación
  - Nodo: Manejado por el controlador a través de SSH

## Playbooks

- Son los archivos donde se escribe el código de Ansible. Se escriben en *YAML*
- Le dicen a Ansible qué ejecutar. Son como una lista de tareas pendientes para Ansible que contiene una lista de tareas. 
- Contienen los pasos que el usuario desea ejecutar en una máquina determinada. 
- Se ejecutan secuencialmente
- Son bloques de construcción para todos los casos de uso de Ansible. 
- La función de un Play es mapear un conjunto de instrucciones definidas contra un anfitrión en particular.

### Etiquetas

- **name** --> Esta etiqueta especifica el nombre del Playbook. Se le puede dar cualquier nombre lógico.
- **Hosts** --> Esta etiqueta especifica las listas de hosts o grupos de hosts con los que queremos ejecutar la tarea. Es obligatoria. Las tareas se pueden ejecutar en la misma máquina o en una máquina remota. Se puede ejecutar las tareas en múltiples máquinas.
- **vars** --> La etiqueta Vars te permite definir las variables que puedes usar en tu PLaynook. El uso es similar a las variables en cualquier lenguaje de programación.
- **tasks** --> Todos los Playbooks deben contener tareas (tasks) o una lista de tareas a ejecutar. Las tareas son una lista de acciones que uno necesita realizar. Cada tarea se vincula internamente a un fragmento de código llamado módulo.
- **notify** --> Son las acciones que se ejecutarán al final de cada tarea en el Playbook. Sólo serán ejecutadas una vez

### Handlers

- Los *Handlers* son la forma de llamar a una *task* una vez que se coompleta otra *task*
- Son parecidos a las *tasks* pero solo se ejecutarán cuendo otra *tasks* lo llame.
- Los *handlers* se ejecutan siempre a final del Playbook, sin importar las *tasks* que haya en medio.

## Inventarios

-  Podemos definir a grupos de servidores o dispositivos, para que ejecute a un grupo completo.
- es uan descripción de los nodos acesibles por Ansible. El inventario se define en un fichero de confirmación en formato INI, la ubicación predeterminada del fichero es */etc/ansible/hosts*
- Pueden ser *Estaticos* o *Dinámicos*

## Estrategias

- **Lineal** (Default): Se espera a que todos los nodos ejecuten una tarea antes de continuar con la siguiente
- **Batch**: Permite correr en lotes. Los lotes pueden especificarse de distintas formas.
- **Free**: Hace que cada nodo corra las tareas tan rápido como pueda, de forma independiente
- **Debug**: Abrirá un debugger cuando una tarea falle.

- La estrategia se especifica en el playbook

## Roles

- Son conjuntos de tareas que se organizan de forma que pueden
reutilizarse. Permiten tener más organización con los playbooks, y pueden ser compartidos.
- Se puede tener un directorio con roles comunes

## Módulos

- Los módulos ofrecen las funciones que puedes ejecutar con los tasks.
Existen módulos incluidos con el sistema (core), los adicionales (extras) y es posible desarrollar módulos propios.

- **Core** --> Los módulos *core* incluyen las funciones más habituales como gestión de paquetes, de usuarios, de repositorios. 
- **Extra** --> https://github.com/ansible/ansible-modules-extras

## Callbacks

- Los Callback plugin son un tipo de plugin que gestionan la salida y los eventos que se producen.

## Facts

- Cada vez que corres un plugin, por defecto Ansible ejecutará un módulo llamado setup que recoge algunos datos, llamados facts.
- Por defecto sólo se almacenan durante la ejecución pero es posible almacenarlos en redis o en ficheros json.


## Orquestación

- Incluye unos módulos que permiten gestionar ejecución en distintos
nodos.
  - *delegate_to (o local_action)*
  - *wait_for o run_once*
  - Son herramientas que pueden hacernos más fácil la orquestación.


## Recomendaciones

- Define tus servidores en “proyectos”.
- Usa un fichero de configuración por proyecto.
- Ten un directorio de roles común para todos tus proyectos.
- Utiliza un known_hosts por proyecto.
- Si es posible, usa inventarios dinámicos.

## Ecosistemas

- Ansible Galaxy --> Versión de software libre del software que gestiona Ansible Galaxy. Puede servir para que un grupo comparta de forma limita los roles.
- Ansible tower --> Herramienta comercial para gestionar Ansible. 
- Ansible ARA --> Herramienta open source que almacena todas las ejecuciones y sus salidas en una base de datos, de forma que puede ser accedido por una GUI
- Ansistrano --> Ansistrano es un port de Capistrano para Ansible. Está disponible en Ansible Galaxy y consiste en 2 nodos en los que mediantes variables configuras la instalación, actualización y todo lo relacionado con el despliegue de software.
- Gestores de tareas --> Rundeck y Gunnery son herramientas de software libre para gestionar la gestión de tareas. Pueden utilizarse como alternativas a Ansible Tower.

## Contras

- Más de 3800 issues abiertas
- Documentación mejorable, mucha pero desordenada e inconsistente
- Los módulos tiene documentación inconsistente y no siempre actualizada.

---

# Apache en Ansible

- Crear la carpeta que contendrá los archivos de configuración
  > mkdir ansible-apache
- Crear el fichero  *ansible.cfg*
  > vi ansible.cfg


---

# Comandos

- Caracteristicas del nodo
  > ansible -m command -a 'hostnamectl' 192.168.5.7


---

# Bibliografia

- [Tutorial ansible](https://blog.deiser.com/es/primeros-pasos-con-ansible)
- [Video Mario Pérez](https://www.youtube.com/watch?v=uqMkIdLE0AQ)
- [Instalar phyton](https://www.ochobitshacenunbyte.com/2018/11/21/instalar-python-3-en-centos-7-desde-las-fuentes/)
- [Tutorial inventarios](https://blogvirtualizado.com/inventarios-de-ansible/)
- [Pablo Martínez Tutorial](http://docecosas.com/presentaciones/Conociendo%20Ansible.pdf)
- [Tutoruial instalar apache](https://www.digitalocean.com/community/tutorials/how-to-configure-apache-using-ansible-on-ubuntu-14-04#step-1-%E2%80%94-configuring-ansible)
- [Ejemplo playbook apache](https://www.server-world.info/en/note?os=CentOS_7&p=ansible&f=3)
- [Ejemplo 2 playbook apache](https://linoxide.com/tools/setup-ansible-centos-7/)