# Inyecciónes sql automatizadas usando SQLMAP

## TEMAS 


* [Introducción](#Introducción)
* [¿Qué es sqlmap?](#¿Qué-es-sqlmap?)
* [Instalación de sqlmap](#Instalación-de-sqlmap)
* [Uso básico de sqlmap](#Uso-básico-de-sqlmap)
  * [Método GET](#Método-GET)
  * [Método POST](#Método-POST)



## ¿Qué es sqlmap?

SQLMap es una herramienta para explotar la vulnerabilidad de SQL injection. Esta herramienta automatiza el ataque para así explotar la página.


## Instalación de sqlmap

Para empezar me gustaría dejar la página oficial aquí: `http://sqlmap.org/`

Sqlmap es una herramienta que funciona en python en sus versiones: 2.6, 2.7 y 3.x en todas las plataformas, así que no hay problema para usarlo, personalmente lo he usado en windows, linux y en termux y en todas funciona exelente.

Lo primero que tenemos que hacer es tener instalado git para poder clonar su repositorio oficial a nuestro dispositivo, el sitio en git es el siguiente:

https://github.com/sqlmapproject/sqlmap.git

para clonarlo usamos lo siguiente:

git clone --depth 1 https://github.com/sqlmapproject/sqlmap.git sqlmap-dev

una vez que tengamos clonado el repositorio entramos a la carpeta `sqlmap-dev` y ejecutamos el archivo `sqlmap.py`:

`python sqlmap.py`


## Uso básico de sqlmap

Para ver las opciones de ayuda de esta herramienta basta con usar lo siguiente:

`sqlmap.py -h`

Lo que nos devolvera las opciones basicas para hacer un correcto uso de esta herramienta, algo que se tiene que entender bien es el correcto orden de ejecuion para agregar las opciones:

`sqlmap.py --opcion -u URL`

Algo que tenemos que tener en cuenta es que esta herramienta nos permite hacer escaneos desde el método GET y POST:

### Método GET

Sqlmap automaticamente usa el metodo GET para trabajar, la sintaxis nos queda de la siguiente manera:

`sqlmap.py -u "http://www.paginaparaejemplo.com/algo.php?id=1&id2=1&id3=1"`

Algo muy importante a tener en cuenta es que sqlmap escanea la página parametro por paramerto, esto significa que si tenemos una página con mas de un parametro tenemos que especificar el puerto que queremos analizar o escanear todos uno a uno.


### Método POST

Para usar el método POST tenemos que decirle a la herramienta que utilizarémos este método, para eso usamos:

`--method POST`

Despues de declarar que usarémos el método post tenemos que declarar los parametros que vamos a analizar, para eso usamos:

`--data`

Ejemplo:

`sqlmap.py -u "http://www.paginaparaejemplo.com/algo.php" --method POST --data "id=1&id2=1&id3=1"`




