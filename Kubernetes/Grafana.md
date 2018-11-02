# Grafana

## Instalarlo

  > sudo yum install https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana-5.3.1-1.x86_64.rpm 

## Servicio

- Lanzar el servicio (systemctl)
  > sudo service grafana-server start  
- Comprobarlo
  > sudo systemctl daemon-reload  
  > sudo systemctl start grafana-server  
  > sudo systemctl status grafana-server

## Acceder

- Lanzarlo
  > sudo iptables -t nat -A PREROUTING -p tcp --dport 9001 -j REDIRECT --to-port 3000
- Desde el navegador 
  > http://10.45.17.193:9001/

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

## Grafana CLI

> grafana-cli admin