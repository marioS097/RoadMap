# Kubernetes

## Instalación

1. Primero actualizar los paquetes
    > sudo yum update
2. Necesario tener docker
    - **Importante** Version 17.03
    ``` CentOS 7
    sudo yum install --setopt=obsoletes=0 \
    docker-ce-17.03.2.ce-1.el7.centos.x86_64 \
    docker-ce-selinux-17.03.2.ce-1.el7.centos.noarch # on a new system with yum repo defined, forcing older version and ignoring obsoletes introduced by 17.06.0
    ```
    > sudo systemctl status docker.service
    - En caso de error al inicar el servicio, borrar el contenido de */var/lib/docker*
        > sudo rm -rf /var/lib/docker
3. Instalar *kubeadm*, *kubelet* and *kubectl*
    ``` CentOS 7
    sudo cat <<EOF > /etc/yum.repos.d/kubernetes.repo
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
    - Permanentemente
      > sudo vi /etc/fstab
      - Comentar la linea de Swap
      > mount -a
    - Temporalmente
      > sudo swapoff -a
    - En el servicio de kubernetes
      - /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
      > Environment="KUBELET_EXTRA_ARGS=--fail-swap-on=false"
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
5. Crear la subred
   > kubectl apply -f "<https://cloud.weave.works/k8s/net?k8s-version=$(kubectl> version | base64 | tr -d '\n')"
   - Comprobar que está funcionando
     > kubectl get pods --all-namespaces
6. Comprobar el sericio
    > sudo systemctl status kubelet
7. Comprobar que se ha levantado el nodo
    > kubectl get nodes
8. Añadir los servidores
   - En el resto de maquinas
   > kubeadm join --token 350120.fdd5be57c172dca1 192.168.0.216:6443 --discovery-token-ca-cert-hash sha256:d7b06c32c6c414606aa573ea86124e843497676e69867cf2a76420d4107a238e
9. Comprobar que se han añadido correctamente (Desde la maquina *Master*)
    > kubectl get nodes

## Lanzar un despliegue

1. Escribir el despliegue en *yaml*
   > vi nginx-deployment.yaml
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
   - **NAME:** Enumera los nombres de los desliegues
   - **DESIRED:** Muestra el número deseado de réplicas de la aplicación
   - **CURRENT:** Muestra cuántas réplicas se están ejecutando actualmente
   - **UP-TO-DATE:** Muestra el número de réplicas que se han actualizado para lograr el estado deseado
   - **AVIABLE:** Muestra cuántas réplicas de la aplicación están disponibles
   - **AGE:** Muestra la cantidad de tiempo que la aplicación ha estado ejecutándose
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

## Escalar un despliegue

> kubectl scale deployment nginx-deployment --replicas=10
- También se puede configurar para que autoescale
  > kubectl autoscale deployment nginx-deployment --min=10 --max=15 --cpu-percent=80
- Comprobarlo
  > kubectl get deployments
- Comprobar las normas activas de autoescalados
  > kubectl get hpa
- Eliminar las normas de autoescalado
  > kubectl delete hpa nginx-deployment

## Espacios de nombres (Namespaces)

- Ver los *namespaces*
  > kubectl get namespaces
- Establecer temporalmente el espacio de nombres para una solicitud
  > kubectl --namespace=[*namespace*] run nginx
  > kubectl --namespace=[*namespace*] get pods
- Guardar permannente el *namespaces* para todos los comandos kubectl posteriores
  > kubectl config set-context $(kubectl config current-context) --namespace=[*namespace*]
  - Comprobar que se ha guardado
    > kubectl config view | grep namespace:

### Crear un namespace

1. Crear un nuevo YAML
   > nano my-namespace.yaml

   ``` YAML
   apiVersion: v1
   kind: Namespace
   metadata:
     name: mario-namespace
     labels:
       name:
         mario-label
    ```

   > kubectl create -f ./my-namespace.yaml
2. Comprobar que se ha creado
   > kubectl get namespaces --show-labels

### Eliminar un *namespace*

- Eliminar un *namespace*
  > kubectl delete namespaces *[namespace]*

---

## Actualizar cluster entero

1. Comprobar la lista de versiones
   > sudo yum list --showduplicates kubeadm --disableexcludes=kubernetes
2. En el master actualizar el *kubeadm*
   > yum install -y kubeadm-1.13.x-0 --disableexcludes=kubernetes
3. Verificar que la descarga funcione y tenga la versión esperada
   > kubeadm version
4. En el master ejecutar este comando
   > kubeadm upgrade plan
5. Elija una versión para actualizar y ejecute el comando apropiado
   > kubeadm upgrade apply v1.x.0
6. Tendría que salir un resultado parecido a este
   ```
   [upgrade/successful] SUCCESS! Your cluster was upgraded to "v1.14.0". Enjoy!

   [upgrade/kubelet] Now that your control plane is upgraded, please proceed with upgrading your kubelets if you haven't already done so.
   ```
7. Actualice el kubelet en el nodo del plano de control
   > yum install -y kubelet-1.x.x-0 --disableexcludes=kubernetes
8. **Actualizar kubectl en todos los nodos(workers)**
   > yum install -y kubectl-1.13.x-0 --disableexcludes=kubernetes
9. Preparar el nodo marcandolo como *unshedulable* y desalojando las cargas de trabajo
   > kubectl drain *[nodo]* --ignore-daemonsets
10. En cada nodo, excepto el nodo manager, actualice la configuración de *kubelet*
    > kubeadm upgrade node config --kubelet-version v1.13.x
11. Actualizar la versión del paquete Kubernetes en cada nodo 
    > yum install -y kubelet-1.13.x-0 kubeadm-1.13.x-0 --disableexcludes=kubernetes
12. Reiniciar el proceso de *kubelet* en todos los nodos
    > systemctl restart kubelet
13. Volver a marcar el noco como *shedulable*
    > kubectl uncordon *[nodo]*
14. Una vez que se haya actualizado kubelet en todos los nodos, verificar que todos los nodos estén disponibles
    > kubectk get nodes

--- 

### Comando *kops*

- *Kops* es un proyecto oficial de Kubernetes para la gestión de clusters de Kubernetes de grado de producción.

## Comandos útiles

- **Quitar nodos**
  - Desde el master
  > kubectl drain *[node name]* --delete-local-data --force --ignore-daemonsets  
  > kubectl delete node *[node name]*  
  > kubeadm reset
- **Listar los *token***
    > sudo kubeadm token list
- **Cambiar el número de replicas**
  > kubectl scale deployment nginx-deployment --replicas=10
- **Abrir la consola de pod**
  > kubectl exec -it *[container]* -- /bin/bash
- **Comando para sacar join exacto, desde el Master** 
  > kubeadm token create --print-join-command
- **Comando join del nodo**
  > sudo kubeadm join *[ip]:6443* --token *[token]* --discovery-token-ca-cert-hash *[hash]*
- **instalar version especifica**
  > sudo yum install -y kubeadm-1.13.5 kubectl-1.13.5 kubelet-1.13.5 --disableexcludes=kubernetes
- **Ver listado de versiones**
  > yum list --showduplicates kubeadm --disableexcludes=kubernetes


## Eliminar recursos

- **Eliminar un pod utilizando el tipo y el nombre en especifico del *pod.json***
  > kubectl delete -f ./pod.json
- **Eliminar pods y servicios con los mismos nombres "baz" y "foo"**
  > kubectl delete pod,service baz foo
- **Eliminar pods y servicios con la etiqueta *myLabel***
  > kubectl delete pods,services -l name=myLabel
- **Eliminar pods y servicios, incluso los no inicializados, con la etiqueta *myLabel***
  > kubectl delete pods,services -l name=myLabel --include-uninitialized
- **Eliminar todos los pods y servicios, incluso los no inicializados, en el *namespace my-ns***
  > kubectl -n my-ns delete po,svc --all
- **Eliminar kubernetes**
  > sudo yum remove -y kubeadm kubectl kubelet kubernetes-cni kube*  
  > sudo yum autoremove -y   
  > sudo rm -rf ~/.kube

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

- Problemas al borrar un namespace, se queda en *Terminating*
  - Comprobar que no tiene recursis asociados (*deployments y pods*)
  - Comprobar llamadas a la API
    > kubectl get crd -n [*namespace*]
    > kubectl get apiservices -n [*namespace*]

- Puertos ocupados, como liberarlos
  > sudo iptables -F

- Si no detecta los paquetes de docker actualizar el repo
  > sudo yum erase docker-engine-selinux


### Todavia no funciona 
- Unable to update cni config: No networks found in /etc/cni/net.d
  - [Ayuda](http://www.openwriteup.com/kubelet-service-is-failing-unable-to-update-cni-config-no-networks-found-in-etc-cni-net-d/)  
  > sudo kubeadm reset   
  > sudo chmod 777 /etc/cni/net.d  
  > sudo systemctl start kubelet.service  

## Bibliografía

- [http://www.javiergarzas.com/2015/07/que-es-docker-sencillo.html](http://www.javiergarzas.com/2015/07/que-es-docker-sencillo.html) (Kubernetes en 10 min)

- [https://www.adictosaltrabajo.com/tutoriales/primeros-pasos-con-kubernetes/\#051](https://www.adictosaltrabajo.com/tutoriales/primeros-pasos-con-kubernetes/#051) (Operaciones básicas de Kubernetes)

- [http://enmilocalfunciona.io/introduccion-a-kubernetes-i/</span>](http://enmilocalfunciona.io/introduccion-a-kubernetes-i/) (Kubernetes con Vagrant y Vbox)
