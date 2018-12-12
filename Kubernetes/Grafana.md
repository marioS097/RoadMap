# Grafana

## Tutorial

- Archivo del despliegue *(Deployment)*
> vi grafana-delpoyment.yaml

``` yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: grafana-core
  labels:
    app: grafana
    component: core
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: grafana
        component: core
    spec:
      containers:
      - image: grafana/grafana:4.2.0
        name: grafana-core
        imagePullPolicy: IfNotPresent
        # env:
        resources:
          # keep request = limit to keep this container in guaranteed class
          limits:
            cpu: 100m
            memory: 100Mi
          requests:
            cpu: 100m
            memory: 100Mi
        env:
          # The following env variables set up basic auth twith the default admin user and admin password.
          - name: GF_AUTH_BASIC_ENABLED
            value: "true"
          - name: GF_SECURITY_ADMIN_USER
            valueFrom:
              secretKeyRef:
                name: grafana
                key: admin-username
          - name: GF_SECURITY_ADMIN_PASSWORD
            valueFrom:
              secretKeyRef:
                name: grafana
                key: admin-password
          - name: GF_AUTH_ANONYMOUS_ENABLED
            value: "false"
          # - name: GF_AUTH_ANONYMOUS_ORG_ROLE
          #   value: Admin
          # does not really work, because of template variables in exported dashboards:
          # - name: GF_DASHBOARDS_JSON_ENABLED
          #   value: "true"
        readinessProbe:
          httpGet:
            path: /login
            port: 3000
          # initialDelaySeconds: 30
          # timeoutSeconds: 1
        volumeMounts:
        - name: grafana-persistent-storage
          mountPath: /var/lib/grafana
      volumes:
      - name: grafana-persistent-storage
        emptyDir: {}
```

- Archivo del servico *(services)*
> vi grafana-service.yaml

```YAML
apiVersion: v1
kind: Service
metadata:
  name: grafana
  labels:
    app: grafana
    component: core
spec:
  type: NodePort
  ports:
    - port: 3000
      nodePort: 30001
  selector:
    app: grafana
    component: core
```

- Archivo con los secretos *(secrets)*
> vi grafana-secret.yaml

 ``` YAML
apiVersion: v1
kind: Secret
data:
  admin-password: YWRtaW4=
  admin-username: YWRtaW4=
metadata:
  name: grafana
type: Opaque
 ```

- **Lanzar los archivos**
> kubectl create -f grafana-deployment.yaml  
> kubectl create -f grafana-service.yaml  
> kubectl create -f grafana-secret.yaml  

## Documentación

- [Grafana GitHub](https://github.com/giantswarm/kubernetes-prometheus/tree/master/manifests/grafana)

---

## Conceptos básicos

- Fuente de datos
  - Grafana admite muchos backends de almacenamiento diferentes para sus datos de series de tiempo (Fuente de datos). Cada fuente de datos tiene un editor de consultas específico que se personaliza para las características y capacidades que la fuente de datos particular expone.
  - Puede combinar datos de múltiples fuentes de datos en un único panel, pero cada panel está vinculado a una fuente de datos específica que pertenece a una organización en particular.
- Organización
  - Grafana es compatible con varias organizaciones para admitir una amplia variedad de modelos de implementación
  - En muchos casos, Grafana se desplegará con una sola Organización.
  - Cada organización puede tener una o más fuentes de datos.
- Usuarios
  - Un usuario es una cuenta con nombre en Grafana.
  - Grafana admite una amplia variedad de formas internas y externas para que los usuarios se autentiquen.
- Fila
  - Una fila es un divisor lógico dentro de un tablero y se usa para agrupar paneles.
- Panel
  - El Panel es el bloque de construcción de visualización básica en Grafana.
  - Actualmente hay cuatro tipos de Panel:
    - **Gráfico:** Este panel le permite graficar tantas métricas y series como desee.
    - **Singlestat:** Requiere una reducción de una sola consulta en un solo número.
    - **Dashlist:** Es un panel especial que no se conectan a ninguna fuente de datos.
    - **Tabla y Texto:** Son paneles especiales que no se conectan a ninguna fuente de datos.
- Editor de consultas (Query editor)
  - Expone las capacidades de su Origen de datos y le permite consultar las métricas que contiene.
  - El panel se actualiza instantáneamente, lo que le permite explorar efectivamente los datos en tiempo real y construir una consulta perfecta para ese Panel en particular.
- Tablero
  - Es donde se junta todo.
  - Los paneles se pueden pensar en un conjunto de uno o más paneles organizados y organizados en una o más filas.
