# Kubernetes

## Instalación

1. Primero actualizar los paquetes
    > sudo yum update
2. Necesario tener docker
    - **Importante** Version 17.03
    ``` CentOS 7
    yum install --setopt=obsoletes=0 \
    docker-ce-17.03.2.ce-1.el7.centos.x86_64 \
    docker-ce-selinux-17.03.2.ce-1.el7.centos.noarch # on a new system with yum repo defined, forcing older version and ignoring obsoletes introduced by 17.06.0
    ```
    > sudo systemctl status docker.service
    - En caso de error al inicar el servicio, borrar el contenido de */var/lib/docker*
        > sudo rm -rf /var/lib/docker
3. Instalar *kubeadm*, *kubelet* and *kubectl*
    ``` CentOS 7
    cat <<EOF > /etc/yum.repos.d/kubernetes.repo
    [kubernetes]
    name=Kubernetes
    baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
    enabled=1
    gpgcheck=1
    repo_gpgcheck=1
    gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
    exclude=kube*
    EOF
    ```
    > setenforce 0  
    > sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes  
    > sudo systemctl enable kubelet && sudo systemctl start kubelet

## Crear un solo clúster maestro con kubeadm

1. Deshabilitar la SWAP
    > sudo nano /etc/systemd/system/kubelet.service.d/10-kubeadm.conf  
    > Environment="KUBELET_EXTRA_ARGS=--fail-swap-on=false"
    - Otra opcion
    > sudo swapoff -a
2. Reiniciar los servicios
   > sudo systemctl daemon-reload  
   > sudo systemctl restart kubelet  
   > sudo systemctl status kubelet
3. **Inicializar el *master***
   > sudo kubeadm init --pod-network-cidr=10.244.0.0/16
4. Funcionar con usuarios no root
   > mkdir -p $HOME/.kube  
   > sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config  
   > sudo chown $(id -u):$(id -g) $HOME/.kube/config
   - Si eres usuario root:
      > export KUBECONFIG=/etc/kubernetes/admin.conf
5. Comprobar el sericio
    > sudo systemctl status kubelet
6. Comprobar que se ha levantado el nodo
    > kubectl get nodes
7. Añadir los servidores
   - Desde otra maquina
   > kubeadm join --token 350120.fdd5be57c172dca1 192.168.0.216:6443 --discovery-token-ca-cert-hash sha256:d7b06c32c6c414606aa573ea86124e843497676e69867cf2a76420d4107a238e
8. Comprobar que se han añadido correctamente
    > kubectl get nodes

## Lanzar un despliegue

1. Escribir el despliegue en *yaml*
   > nano nginx-deployment.yaml
    ``` YAML
    apiVersion: apps/v1
    kind: Deployment
    metadata:
    name: nginx-deployment
    labels:
        app: nginx
    spec:
    replicas: 3
    selector:
        matchLabels:
        app: nginx
    template:
        metadata:
        labels:
            app: nginx
        spec:
        containers:
        - name: nginx
            image: nginx:1.7.9
            ports:
            - containerPort: 8080
    ```
2. Lanzar el despliegue
   > kubectl create -f nginx-deployment.yaml
3. Comprobar que se ha lanzado
   > kubectl get deployments
   - **NAME:** enumera los nombres de los desliegues
   - **DESIRED:** muestra el número deseado de réplicas de la aplicación
   - **CURRENT:** muestra cuántas réplicas se están ejecutando actualmente
   - **UP-TO-DATE:** muestra el número de réplicas que se han actualizado para lograr el estado deseado
   - **AVIABLE:** muestra cuántas réplicas de la aplicación están disponibles
   - **AGE:** muestra la cantidad de tiempo que la aplicación ha estado ejecutándose
4. Ver las *ReplicaSet* creadas en la implementación
   > kubectl get rs
5. Ver las etiquetas generadas en casa *pod*
   > kubectl get pods --show-labels

## Actualizar un despliegue

1. Actualizar *nginx:1.7.9* a *nginx:1.9.1*
   > kubectl set image deployment/nginx-deployment nginx=nginx:1.9.1
   - Con *edit* abrimos el archivo con *vim*
   > kubectl edit deployment/nginx-deployment
2. Ver el estado del despliegue
   > kubectl get deployments
3. Comprobar el historial de modificaciones de un despliegue
   > kubectl rollout history deployment/nginx-deployment
   - Ver los detalles de una revision
   > kubectl rollout history deployment/nginx-deployment --revision=2
4. Volver a una revision anterior
   > kubectl rollout undo deployment/nginx-deployment
   - Voler a una version en especifico
   > kubectl rollout undo deployment/nginx-deployment --to-revision=2

## Comandos útiles

- **Quitar nodos**
  - Desde el master
  > kubectl drain [node name] --delete-local-data --force --ignore-daemonsets  
  > kubectl delete node [node name]  
  > kubeadm reset
- **Listar los *token***
    > sudo kubeadm token list

## Errores

- [ERROR FileContent--proc-sys-net-bridge-bridge-nf-call-iptables]: /proc/sys/net/bridge/bridge-nf-call-iptables contents are not set to 1
  - Editar el archivo y cambiar el *0* por el *1*
  > sudo nano /proc/sys/net/bridge/bridge-nf-call-iptables

- [WARNING SystemVerification]: docker version is greater than the most recently validated version. Docker version: 18.06.1-ce. Max validated version: 17.03
  > sudo yum install docker-ce-17.03.0.ce-1.el7.centos

- [ERROR FileContent--proc-sys-net-bridge-bridge-nf-call-iptables]
  - Editar el fichero de configuración y añadir la siguiente linea
  > sudo nano /etc/sysctl.conf  
  > net.bridge.bridge-nf-call-iptables = 1
  >
  > sudo sysctl -p

- Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "kubernetes")
  - Resetear todo
  > kubeadm reset  
  > yum remove kubelet kubeadm kubectl

## Añadidos

1. Elegir un add-on de red de pod
    1. Instalar un network pod add-on *(Calcio)*
        > kubectl apply -f https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/rbac-kdd.yaml  
        > kubectl apply -f https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/kubernetes-datastore/calico-networking/1.7/calico.yaml
    2. Confirmar que está trabajando
        > kubectl get pods --all-namespaces
2. Instalar **minikube**
    > curl -Lo minikube https://storage.googleapis.com/minikube/releases/v0.29.0/minikube-linux-amd64 && chmod +x minikube && sudo cp minikube /usr/local/bin/ && rm minikube

## Bibliografía

- [http://www.javiergarzas.com/2015/07/que-es-docker-sencillo.html](http://www.javiergarzas.com/2015/07/que-es-docker-sencillo.html) (Kubernetes 10 min)

- [https://www.adictosaltrabajo.com/tutoriales/primeros-pasos-con-kubernetes/\#051](https://www.adictosaltrabajo.com/tutoriales/primeros-pasos-con-kubernetes/#051) (Operaciones básicas de Kubernetes)

- [http://enmilocalfunciona.io/introduccion-a-kubernetes-i/</span>](http://enmilocalfunciona.io/introduccion-a-kubernetes-i/) (Kubernetes con Vagrant y Vbox)
