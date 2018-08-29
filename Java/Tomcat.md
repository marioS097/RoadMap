# Tomcat (XAAMP)

## Error de puerto 8005

Para corregir ese error hay que modificar el fichero de *C:\\xampp\\tomcat\\conf* y modificar la línea *\<Server port="8005" shutdown="SHUTDOWN"\>* cambiando el puerto por otro (8006, por ejemplo) y reiniciar el servicio.

## Instalación en NetBeans

Después de tenerlo instalado y comprobado que funciona:

En NetBeans:

-   *Windows \> Services* o en la pestaña *Services \> Servers*

-   *Add Server*

-   *Apache Tomcat or TomEE *

    -   *Server Location: C:\\xampp\\tomcat* Indicamos la ruta donde está instalado Tomcat

    -   *Username y Password*: Indicamos las credenciales con las que nos conectaremos

    -   Finalizamos

## Creación de proyecto

File \> New Project \> Java Web \> Web Application

Indicar el nombre del Nuevo proyecto. Y si es necesario marcar *Spring web MVC* para utilizar el freamwork
