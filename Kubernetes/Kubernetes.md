# Kubernetes

Es un proyecto para la gestión de aplicaciones en contenedores **Docker**. Permite empaquetar las aplicaciones en contenedores y trasladaros fácil y rápidamente a cualquier equipo para ejecutarlas.

## Objetos de Kubernetes

### Pods 

Un pod **es la unidad mínima que es manejada por Kubernetes**. Este pod es un grupo de uno o más contenedores (normalmente de Docker), con almacenamiento compartido entre ellos y las opciones específicas de cada uno para ejecutarlos. Un modelo de pods específico de una aplicación contiene uno o más contenedores que normalmente irían en la misma máquina.

### Replication Controller 

Un replication controller **se asegura de que grupo de uno o más pods esté siempre disponible**. Si hay muchos pods, eliminará algunos. Si hay pocos, creará nuevos. Un replication controller es al fin y al cabo un supervisor de un grupo de uno o más pods a través de un conjunto de nodos.

### Service

Un service es una **abstracción que define un grupo lógico de pods y una política de acceso a los mismos**. Es la manera de comunicarse los pods debido a que sus IP son variables y si uno se cae y es sustituido por otro.

## Instalación

1. Tener instalado *docker*
2. Crear y editar el fichero *kubernetes.repo*
>sudo vim /etc/yum.repos.d/kubernetes.repo
>
>[kubernetes]
  name=Kubernetes
  baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
  enabled=1
  gpgcheck=1
  repo_gpgcheck=1
  gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
3. Actualizar los paquetes
> sudo yum update
4. Instalar los servicios
> sudo yum install -y kubelet kubeadm kubectl
5. Levantar los servicios
> sudo systemctl enable kubelet && sudo systemctl start kubelet

## Errores
- [ERROR FileContent--proc-sys-net-bridge-bridge-nf-call-iptables]: /proc/sys/net/bridge/bridge-nf-call-iptables contents are not set to 1
  - Editar el archivo y cambiar el *0* por el *1*
- [WARNING SystemVerification]: docker version is greater than the most recently validated version. Docker version: 18.06.1-ce. Max validated version: 17.03
  - sudo yum install docker-ce-17.03.0.ce-1.el7.centos
- kubeadm init --ignore-preflight-errors Swap

- minikube start hyperv

## Bibliografía 

- [<span class="underline">http://www.javiergarzas.com/2015/07/que-es-docker-sencillo.html</span>](http://www.javiergarzas.com/2015/07/que-es-docker-sencillo.html) (Kubernetes 10 min)

- [<span class="underline">https://www.adictosaltrabajo.com/tutoriales/primeros-pasos-con-kubernetes/\#051</span>](https://www.adictosaltrabajo.com/tutoriales/primeros-pasos-con-kubernetes/#051) (Operaciones básicas de Kubernetes)

- [<span class="underline">http://enmilocalfunciona.io/introduccion-a-kubernetes-i/</span>](http://enmilocalfunciona.io/introduccion-a-kubernetes-i/) (Kubernetes con Vagrant y Vbox)