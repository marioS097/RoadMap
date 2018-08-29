# JSON

Acrónimo de *JavaScript Object Notation*, es un formato de texto ligero para el intercambio de datos.

## W3Schools

[<span class="underline">https://www.w3schools.com/js/js\_json\_intro.asp</span>](https://www.w3schools.com/js/js_json_intro.asp)

### Convertir a JSON de

var myJSON = JSON.**stringify**(myObj);

### Convertir a JavaScript

var myObj = JSON.**parse**(myJSON);

### Nombre / valor

"nombre":"valor"

*En JSON, las claves deben ser cadenas, escritas con comillas dobles.*

### Acceder a valores y modificarlos

x = myObj.name; Acceder a un dato.

x = myObj\["name"\]; Acceder a un dato mediante corchetes

myObj.name = "Gilbert"; Modificar un valor

myObj\["name"\] = "Gilbert"; Modificar un valor mediante corchetes

### Tipos de datos

String: “name”:”John”

Números: “age”:30

Objetos: “employee”:{ “name”:”John”, “age”:30, “city”:”New York” }

Arrays: “employees”:\[ “John”, “Anna”, “Peter” \]

Booleans: “sale”:true

Null: “middlename”:null

### Acceder a valores de objetos

### **Usando dot (.)**

myObj = { "name":"John", "age":30, "car":null };

x = myObj.name;

**Usando brackets ( \[ \] )**

myObj = { "name":"John", "age":30, "car":null };

x = myObj\["name"\];

### Borrar valores

**delete** myObj.cars.car2;

### 

### Bucles for-in

**for** (x in myObj) {

document.getElementById("demo").innerHTML += myObj\[x\];

}

### Objetos anidados

var myObj = {

"name":"John",

"age":30,

"cars": {

"car1":"Ford",

"car2":"BMW",

"car3":"Fiat"

}

}

**Acceder a un objeto anidado:**

document.getElementById("demo").innerHTML += **myObj.cars.car2** + "\<br\>";

document.getElementById("demo").innerHTML += **myObj.cars\["car2"\];**

**Acceder a objetos anidados:**

for (i in myObj.cars) {

x += myObj.cars\[i\] + "\<br\>";

document.getElementById("demo").innerHTML = x;

}

### Arrays

**Ejemplo:**

"name":"John",

"age":30,

"cars":\[ "Ford", "BMW", "Fiat" \]

**Acceder a un valor**

x = myObj.cars\[0\];

**Acceder a varios valores mediante un for-in**

for (i in myObj.cars) {

x += myObj.cars\[i\];

}

### Matrices anidadas en objetos

myObj = {

"name":"John",

"age":30,

"cars": \[

{ "name":"Ford", "models":\[ "Fiesta", "Focus", "Mustang" \] },

{ "name":"BMW", "models":\[ "320", "X3", "X5" \] },

{ "name":"Fiat", "models":\[ "500", "Panda" \] }

\]

}

for (i in myObj.cars) {

x += "\<h2\>" + myObj.cars\[i\].name + "\</h2\>";

for (j in myObj.cars\[i\].models) {

x += myObj.cars\[i\].models\[j\] + "\<br\>";

}

}

### JSON.parse()

*Para transformar la información que se recibe de los servidores (siempre en String).*

var obj = JSON.parse('{ "name":"John", "age":30, "city":"New York"}');
