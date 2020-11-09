# Inyecciónes sql automatizadas usando SQLMAP

## TEMAS 


* [Introducción](#Introducción)
* [¿Qué es sqlmap?](#¿Qué-es-sqlmap?)
* [Instalación de sqlmap](#Instalación-de-sqlmap)
* [Uso básico de sqlmap](#Uso-básico-de-sqlmap)



### ¿Qué es sqlmap?

SQLMap es una herramienta para explotar la vulnerabilidad de SQL injection. Esta herramienta automatiza el ataque para así explotar la página.


### Instalación de sqlmap

Para empezar me gustaría dejar la página oficial aquí: `http://sqlmap.org/`

Sqlmap es una herramienta que funciona en python en sus versiones: 2.6, 2.7 y 3.x en todas las plataformas, así que no hay problema para usarlo, personalmente lo he usado en windows, linux y en termux y en todas funciona exelente.

Lo primero que tenemos que hacer es tener instalado git para poder clonar su repositorio oficial a nuestro dispositivo, el sitio en git es el siguiente:

https://github.com/sqlmapproject/sqlmap.git

para clonarlo usamos lo siguiente:

git clone --depth 1 https://github.com/sqlmapproject/sqlmap.git sqlmap-dev

una vez que tengamos clonado el repositorio entramos a la carpeta `sqlmap-dev` y ejecutamos el archivo `sqlmap.py`:

`python sqlmap.py`


### Uso básico de sqlmap

Para ver las opciones de ayuda de esta herramienta basta con usar lo siguiente:

`sqlmap.py -h`

Lo que nos devolvera las opciones basicas para hacer un correcto uso de esta herramienta, algo que se tiene que entender bien es el correcto orden de ejecuion para agregar las opciones:

`sqlmap.py --opcion -u URL`



