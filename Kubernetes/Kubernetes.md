# Kubernetes

## Comandos útiles

- **Quitar nodos**
  - Desde el master
  > kubectl drain [node name] --delete-local-data --force --ignore-daemonsets
  >
  > kubectl delete node [node name]
  >
  > kubeadm reset

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
