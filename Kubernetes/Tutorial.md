# Tutorial Kubernetes

## 1. Crear un clúster de Kubernetes

1. Con minikube instalado, comprobamos la version
    > minikube version
2. Lanzamos el clúster
> minikube start
- Ahora está corriendo el clúster de Kubernetes en la terminal
1. Para interactuar con Kubernetes se utiliza el comando **kubectl**
> kubectl  version
> kubectl cluster-info
- Visualizar los nodos
> kubectl get nodes

## 2. Desplegar una aplicación

1. Lo primero comprobar si está configurado el clúster
    > kubectl version
2. Para ver los nodos del clúster
    > kubectl get nodes
3. Lanzar la aplicación
    - El comando **run** cra unm nuevo despliegue
    - Es enecesario proporcionar un *nombre* y una *imagen* además de especificar el puerto con **--port**
    > kubectl run kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1 --port=8080
4. Ver la lista de despliegues
    > kubectl get deployments
5. Lanzar un proxy
    - En una *nueva terminal* lanzamos el proxy
    > kubectl proxy
6. Ver todas las APIs del host
    > curl http://localhost:8001/version
    - La API del server crea automaticamente un *endpoint* para el pod
7. Primero necesitamos ponerle nombre al POD
    > export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
    >
    > echo Name of the Pod: $POD_NAME
8. Ahora podemos hacer una solicitud HTTP a la aplicación que se ejecuta en ese pod
    > curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/proxy/

## 3. Esplorar la aplicación

1. Conocer los Pods existentes
    > kubectl get pods
2. Ver que contenedores contienen los Pods
    > kubectl describe pods
    - Podemos ver las direcciones IP, los puesrtos y una lista de eventos del Pod
3. Para poder interactuar con la aplicación necesitamos conectarnos con ella mediante network
    - En una *nueva terminal* lanzamos el proxy
    > kubectl proxy
4. Obtenemos el nombre del Pod y lo consultamos a traves del proxy
    - Obtenemos el Pod y lo almacenamos en la variable de entorno **POD_NAME**
    > export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
    >
    >echo Name of the Pod: $POD_NAME
5. Para poder ver la salida de la aplicación utilizamos el comando **curl**
    > curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/proxy/
6. Listar los *logs* de un Pod
    > kubectl logs $POD_NAME
7. Listar las variables de entorno
    > kubectl esec $POD_NAME env
8. Si el Pod está levnatado y corriendo podemos ejecuta comandos directamente en el contenedor
    - Lanzamos el *bash*
    > kubectl exec -it $POD_NAME bash
9. Con la consola abierta en el contenedor donde ejecutamos nuestra aplicación veremos el código fuente de la aplicación
    - Dentro de la *terminal del contenedor*
    > cat server.js
10. Comprobar que la aplicación está corriendo
    - Dentro de la *terminal del contenedor*
    > curl localhost:8080
11. Salir del contenedor
    - Dentro de la *terminal del contenedor*
    > exit

## 4. Exponer la aplicación públicamente

1. Verficar que nuetsra aplicación está corriendo
    > kubectl get pods
2. Listar los servicios actuales del clúster
    > kubectl get services
3. Exponer el servicio
    > kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 8080
4. Volvemos a listar los servivicios
    > kubectl get services
    - Saldrá un nuevo servicio llamado *kubernetes-bootcamp*
5. Conocer los puertos abiertos al exterior
    > kubectl describe services/kubernetes-bootcamp
6. Crear una variable de entorno llamada **NODE_PORT**
    > export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
    >
    > echo NODE_PORT=$NODE_PORT
7. Comprobar que la aplicación está expuesta fuera del clúster
    > curl $(minikube ip): $NODE_PORT
8. El despliegue crea automáticamente una etiqueta para el Pod
    > kubectl describe deployment
9. Utilizando la etiqueta consultamos nuestra lista de Pods
    > kubectl get pods -l run=kubernetes-bootcamp
10. Listar la lista de servicios existentes
    > kubectl get services -l run=kubernetes-bootcamp
11. Obtener le nombre del Pod y almacenarlo en la variable **POD_NAME**
    > export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
    >
    > echo Name of the Pod: $POD_NAME
12. Aplicar la etiqueta
    > kubectl label pod $POD_NAME app=v1
13. Comprobarlo
    > kubectl describe pods $POD_NAME
14. Comprobar lista de Pods con la etiqueta
    > kubectl get pods -l app=v1
15. Eliminar un servicio
    > kubectl delete service -l run=kubernetes-bootcamp
16. Lo confirmamos
    > kubectl get services
17. Comprobar que la ruta ya no está expuesta
    > curl $(minikube ip): $NODE_PORT
    - Debría de salir un mensaje de error:  
    - *curl: (7) Failed to connect to 172.17.0.119 port 30979: Connection refused*
18. Aunque no se pueda acceder dese fuera del clúster, se puede comprobar que la aplicación sigue corriendo
    > kubectl exec -ti $POD_NAME curl localhost:8080

## 5. Escalar la aplicación

1. Listar los despliegues
    > kubectl get deployments
    - Se mostrará distintas opciones
        - *DESIRED* Número de replicas
        - *CURRENT* Número de replicas que estan corriendo
        - *UP-TO-DATE* Número de réplicas que se actualizaron para coincidir con el estado deseado (configurado)
        - *AVAILABLE* Número de replicas que están disponibles para usarse
2. Escalar la aplicación a 4 replicas
    > kubectl scale deployments/kubernetes-bootcamp --replicas=4
3. Volver a listar los despliegues
    >  kubectl get deployments
    - Se han aplicado los cambios y ahora tenemos 4 instancias de la aplicación
4. Comprobar el número de Pods cambiados
    > kubectl get pods -o wide
    - Ahora tenemos 4 Pods con diferentes direcciones IP
5. Comprobar que se han registrado los cambios en *Deployment events log*
    > kubectl describe deployments/kubernetes-bootcamp
6. Crear una variable de entorno llamada *NODE_PORT* que tendra el valor del puerto del nodo
    > export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
    >
    > echo NODE_PORT=$NODE_PORT
7. Hacer un *curl* para ver la IP expuesta y el puerto
    > curl $(minikube ip): $NODE_PORT
8. Volver a escalar a 2 replicas
    > kubectl scale deployments/kubernetes-bootcamp --replicas=2
9. Listar los despliegues  
    > kubectl get deployments
10. Comprobar el número de Pods
    > kubectl get pods -o wide

## 6. Actualizar la Aplicación

1. Listar los despliegues
    > kubectl get deployments
2. Listar los Pods que estan en eejecución
    > kubectl get pods
3. Ver ka version de la aplicación utilizando el comando **describe**
    > kubectl describe pods
4. Actualizar la aplicación a la versión 2 usando el comando **set image**
    > kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2
    - El comando notifica que el despliegue usa una imagen diferente e inicia una actialización progresiva
5. Comporbar el estado de los nuevos Pods
    > kubectl get pods
6. Comprobar que la aplicación está corriendo
    > kubectl describe services/kubernetes-bootcamp
7. Crear una variable de entorno llamada *NODE_PORT* que tendrá el valor del puerto del nodo asigando
    > export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
    >
    > echo NODE_PORT=$NODE_PORT
8. Hacer un *curl* para ver la IP expuesta y el puerto
    > curl $(minikube ip): $NODE_PORT
    - Comprobar que está corriendo en la *version 2*
9. También se puede confirmar ejecutando un comando de estado de despliegue
    > kubectl rollout status deployments/kubernetes-bootcamp
10. Ver la versión de la imagen actual de la aplicación
    > jubectl descirbe pods
11. Realizar otra actualización
    > kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=gcr.io/google-samples/kubernetes-bootcamp:v10
12. Ver el estado de los despliegues
    > kubectl get deployments
13. Si hay el error de no tener el número de Pods disponibles, listar los Pods de nuevo
    > kubectl get pods
14. Usar el comando **describe** dará más información
    > kubectl describe pods
15. Si no hay ninguna imagen llamada *v10*, volver a la version anterior
    > kubectl rollout undo deployments/kubernetes-bootcamp
    - El comando **rollout** revierte el despliegue al estado anterior conocido
16. Volver a listar los Pods
    > kubectl get pods
17. Comprobar la imagen desplegada en ellos
    > kubectl describe pods