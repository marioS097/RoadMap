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
    > sudo systemctl enable kubelet && systemctl start kubelet

## Crear un solo clúster maestro con kubeadm

1. Deshabilitar la SWAP
    > sudo nano /etc/systemd/system/kubelet.service.d/10-kubeadm.conf  
    > Environment="KUBELET_EXTRA_ARGS=--fail-swap-on=false"
2. Reiniciar los servicios
   > sudo systemctl daemon-reload  
   > sudo systemctl restart kubelet  
   > sudo systemctl status kubelet
3. **Inicializar el *master***
   > kubeadm init
4. Elegir un add-on de red de pod
    1. Instalar un network pod add-on *(Calcio)*
        > kubectl apply -f https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/rbac-kdd.yaml  
        > kubectl apply -f https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/kubernetes-datastore/calico-networking/1.7/calico.yaml
    2. Confirmar que está trabajando
        > kubectl get pods --all-namespaces
5. Funcionar con usuarios no root
   > mkdir -p $HOME/.kube  
   > sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config  
   > sudo chown $(id -u):$(id -g) $HOME/.kube/config
   - Si eres usuario root:
      > export KUBECONFIG=/etc/kubernetes/admin.conf
6. Comprobar el sericio
    > sudo systemctl status kubelet
7. Comprobar que se ha levantado el nodo
    > kubectl get nodes
8. Añadir los servidores
   > kubeadm join --token 350120.fdd5be57c172dca1 192.168.0.216:6443 --discovery-token-ca-cert-hash sha256:d7b06c32c6c414606aa573ea86124e843497676e69867cf2a76420d4107a238e
9. Comprobar que se han añadido correctamente
    > kubectl get nodes

## Comandos útiles

- **Quitar nodos**
  - Desde el master
  > kubectl drain [node name] --delete-local-data --force --ignore-daemonsets  
  > kubectl delete node [node name]  
  > kubeadm reset
- Listar los *token*
    > sudo kubeadm token list

## Errores

- [ERROR FileContent--proc-sys-net-bridge-bridge-nf-call-iptables]: /proc/sys/net/bridge/bridge-nf-call-iptables contents are not set to 1
  - Editar el archivo y cambiar el *0* por el *1*
- [WARNING SystemVerification]: docker version is greater than the most recently validated version. Docker version: 18.06.1-ce. Max validated version: 17.03
  - sudo yum install docker-ce-17.03.0.ce-1.el7.centos
- kubeadm init --ignore-preflight-errors Swap

- minikube start hyperv

## Bibliografía

- [http://www.javiergarzas.com/2015/07/que-es-docker-sencillo.html](http://www.javiergarzas.com/2015/07/que-es-docker-sencillo.html) (Kubernetes 10 min)

- [https://www.adictosaltrabajo.com/tutoriales/primeros-pasos-con-kubernetes/\#051](https://www.adictosaltrabajo.com/tutoriales/primeros-pasos-con-kubernetes/#051) (Operaciones básicas de Kubernetes)

- [http://enmilocalfunciona.io/introduccion-a-kubernetes-i/</span>](http://enmilocalfunciona.io/introduccion-a-kubernetes-i/) (Kubernetes con Vagrant y Vbox)
