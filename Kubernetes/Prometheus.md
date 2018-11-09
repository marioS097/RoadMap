# Prometheus

## Connectar a Prometheus

- Se puede conectar el depliegue con Prometheus de dos maneras
  1. Usando *Kubectl port forwarding*
  2. Exponer el depliegue de Prometheus como un servicio con *NodePort* o un equilibrador de carga
- Usando *Kubectl Port Forwarding*
  - Primero, conocer el nombre del pod de Prometheus
    > kubectl get pods --namespace=monitoring
  - Ejecutar el siguiente comando con el nombre de tu pod para acceder a Prometheus desde el puerto 9100 *(Remplazar XXXXXX-XXXXXX por el nombre del pod)*
    > kubectl port-forward prometheus-monitoring-XXXXXX-XXXXXX 8080:9100 -n monitoring
- Exponer como un servicio
  - Crear un archivo llamado *prometheus-service.yaml*. Expondremos Prometheus en el puerto 9100
    ``` yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: prometheus-service
    spec:
      selector:
        app: prometheus-server
      type: NodePort
      ports:
        - port: 8080
          targetPort: 9200
          nodePort: 30000
    ```
  - Exponerlo con el siguiente comando
    > kubectl create -f prometheus-service.yaml --namespace=monitoring
  - Una vez creado, puede acceder al panel de Prometheus utilizando cualquier IP de nodo Kubernetes en el puerto 9200