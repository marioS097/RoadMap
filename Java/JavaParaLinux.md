# Java para Linux

## Instalación

1.  Comprobar la versión de Java instalada : **java –version**

2.  Instala el JRE: **sudo apt-get install default-jre**

3.  Instala JDK: **sudo apt-get install default-jdk**

4.  Ruta donde se ha instalado Java: **sudo update-alternatives –config java**

    1.  Seleccionar la ruta y copiarla

5.  Editar el fichero para añadirla variable: **sudo nano /etc/enviroment**

    1.  Declarar la variable *JAVA\_HOME*

    2.  *JAVA\_HOME=”\[Ruta donde se ha instalado Java (Que la tenemos copiada)\]”*

    3.  Pulsamos *Ctrl+O* para guardar y salimos con *Ctrl+X*

6.  Refrescar el fichero: **source /etc/enviroment** :

7.  Comprueba que la variable se ha guardado correctamente: **Echo $JAVA\_HOME**

## Crear el fichero *Hello World*

1.  Crear la carpeta donde se contenga el proyecto: **mkdir HelloWorld**

2.  Entrar en el directorio: **cd HelloWorld**

3.  Crear el fichero *HelloWorld.java*: **touch HelloWorld.java**

4.  Editar el fichero *HelloWorld.java*: **nano HelloWorld.java**

> public class HelloWorld {
>
> public static void main(String\[\] args) {
>
> // Prints "Hello World" to the terminal window.
>
> System.out.println("Hello World");
>
> }
>
> }

1.  Compilar el proyecto: **javac HelloWorld.java**

2.  Comprobar que se ha compilado correctamente (Tiene que estar el archivo *HelloWorld.class)*: **ls**

3.  Ejecutar el archivo: **java HelloWorld**

## Bibliografía

[<span class="underline">https://www.youtube.com/watch?v=h6AZ8zopwUc</span>](https://www.youtube.com/watch?v=h6AZ8zopwUc) (Instalación de Java)

[<span class="underline">https://www.youtube.com/watch?v=Vx0Iw9MgXsI</span>](https://www.youtube.com/watch?v=Vx0Iw9MgXsI) (Compilar y Ejecutar JAVA en Ubuntu)
