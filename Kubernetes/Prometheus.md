# Prometheus

## Instalación de Prometheus

1. Tener actualizados los repositorios
    > sudo yum update -y
2. Ir a la página oficial de [Prometheus](https://prometheus.io/download/) y descargar el archivo binario
3. Crear los directorios necesarios
   > sudo mkdir /etc/prometheus  
   > sudo mkdir /var/lib/prometheus
4. Descargar la fuente usando *curl*, descomprímala y cambiar el nombre de la carpeta extraída a *prometheus-files*.
    > curl -LO [curl -LO https://github.com/prometheus/prometheus/releases/download/v2.3.2/prometheus-2.3.2.linux-amd64.tar.gz](https://github.com/prometheus/prometheus/releases/download/v2.3.2/prometheus-2.3.2.linux-amd64.tar.gz)
    > tar -xvf prometheus-2.3.2.linux-amd64.tar.gz  
    > mv prometheus-2.3.2.linux-amd64 prometheus-files
5. Copiar los archivos binarios prometheus y promtool de la carpeta prometheus-files a /usr/local/bin
    > sudo cp prometheus-files/prometheus /usr/local/bin/  
    > sudo cp prometheus-files/promtool /usr/local/bin/
6. Mover los directorios de *consoles* y *console_libraries* de *prometheus-files* a /etc/prometheus
    > sudo cp -r prometheus-files/consoles /etc/prometheus  
    > sudo cp -r prometheus-files/console_libraries /etc/prometheus

## Configurar el Prometheus

- Crear el fichero *prometheus.yml*
  > sudo vi /etc/prometheus/prometheus.yml

  ``` YAML
  global:
    scrape_interval: 10s

  scrape_configs:
    - job_name: 'prometheus'
      scrape_interval: 5s
      static_configs:
        - targets: ['localhost:9090']
  ```

## Configurar el archivo de servicio Prometheus

- Crear el fichero *prometheus-deployment.yaml*
  > vi /etc/prometheus/prometheus-deployment.yaml

  ``` YAML
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    name: prometheus-deployment
  spec:
    replicas: 1
    template:
      metadata:
        labels:
          app: prometheus-server
      spec:
        containers:
          - name: prometheus
            image: prom/prometheus:v2.1.0
            args:
              - "--config.file=/etc/prometheus/prometheus.yml"
              - "--storage.tsdb.path=/prometheus/"
            ports:
              - containerPort: 9090
            volumeMounts:
              - name: prometheus-config-volume
                mountPath: /etc/prometheus/
              - name: prometheus-storage-volume
                mountPath: /prometheus/
        volumes:
          - name: prometheus-config-volume
            configMap:
              defaultMode: 420
              name: prometheus-server-conf

          - name: prometheus-storage-volume
            emptyDir: {}
    ```
- Crear un despliegue utilizando el archivo anterior
  > kubectl create -f prometheus-deployment.yaml
- Comprobar que se ha desplegado
  > kubectl get deployments

## Connectar a Prometheus

- Se puede conectar el depliegue con Prometheus de dos maneras
  1. Usando *Kubectl port forwarding*
  2. Exponer el depliegue de Prometheus como un servicio con *NodePort* o un equilibrador de carga

1. Usando *Kubectl Port Forwarding*  
    - Primero, conocer el nombre del pod de Prometheus
      > kubectl get pods --namespace=monitoring
    - Ejecutar el siguiente comando con el nombre de tu pod para acceder a Prometheus desde el puerto 9100 *(Remplazar XXXXXX-XXXXXX por el nombre del pod)*
      > kubectl port-forward prometheus-monitoring-XXXXXX-XXXXXX 8080:9100 -n monitoring
2. Exponer como un servicio
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
          targetPort: 9090
          nodePort: 30000
    ```
    - Exponerlo con el siguiente comando
    > kubectl create -f prometheus-service.yaml --namespace=monitoring
    - Una vez creado, puede acceder al panel de Prometheus utilizando cualquier IP de nodo Kubernetes en el puerto 9200

## Bibliografia

- [Devopscube](https://devopscube.com/install-configure-prometheus-linux/)