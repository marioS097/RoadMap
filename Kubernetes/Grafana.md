# Grafana

## Install

- Install
  > sudo yum install https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana-5.3.1-1.x86_64.rpm 

## Service

- Start the server (via systemd)
  > sudo service grafana-server start  
- Comprobarlo
  > sudo systemctl daemon-reload  
  > sudo systemctl start grafana-server  
  > sudo systemctl status grafana-server

## Acceder

- Desde el explorador
  > sudo iptables -t nat -A PREROUTING -p tcp --dport 9001 -j REDIRECT --to-port 3000

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