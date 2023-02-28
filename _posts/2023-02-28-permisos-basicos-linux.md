---
title: 'Permisos básicos en Linux: Gestión de permisos mediante chmod'
layout: post
post-image: https://cdn.pixabay.com/photo/2018/02/11/15/15/ubuntu-3145957_960_720.png
description: En este post veremos cómo establecer permisos en Linux usando chmod. Explicaremos los dos posibles métodos para la implentación de permisos y algún que otro tip para recordar cómo establecerlos mediante el método numérico de chmod.
date: 2023-02-28
tags: 
- sysadmin 
- linux 
- chmod
- server
- permisos
---

Los permisos en sistemas empresariales es uno de los aspectos más importantes y lo que más vas a tener que usar, por tanto, es importante que sepas su funcionamiento. Estos permisos nos permiten establecer reglas en directorios o archivos para que determinados usuarios o grupos de usuarios puedan acceder, leer o escribir. En Linux el comando a usar para establecer estos permisos es CHMOD, pero a veces puede ser confuso a la hora de aplicarlo. En esta entrada al blog veremos cómo administrar correctamente estos permisos en Linux.

# ¿Cómo funcionan los permisos en Linux?

Cuando miramos los permisos que tiene un directorio o archivo, primero vemos que hay **cuatro bloques de permisos**, estos bloques son **tipo, propietario, grupo y otros permisos.**

El **primer carácter** de la cadena indica el **tipo de archivo** y se puede representar con los siguientes caracteres dependiendo de qué tipo de archivo tengamos entre manos:
- d: Directorio
- b: Archivo de bloque
- c: Archivo especial de caracteres
- p: Canal
- s: Socket
- -: Archivo normal

Detrás del primer carácter veremos que existen otros **9 caracteres** que están divididos en **tres bloques diferenciados**. Como hemos dicho anteriormente, después del primer carácter que indica el tipo, tenemos el **primer bloque** que nos indica el **propietario** del directorio o archivo. Aquí se pueden establecer los permisos que requiera el que será el propietario de este directorio o archivo. 
En el **segundo bloque** hablaremos del **grupo de usuarios** que tiene permiso sobre este directorio o archivo y finalmente, en el **último bloque**, tendremos al **resto de usuarios**, es decir, los usuarios que no sea ni el propietario ni estén englobados en el grupo de usuarios que tienen permisos sobre este directorio o archivo.

Para representar los diferentes permisos que tienen los 3 bloques se usan los caracteres siguientes:
- r: Capacidad de lectura y navegar por la carpeta si es un directorio.
- w: Capacidad de escritura dentro del archivo o directorio.
- x: Capacidad de ejecución.
- -: Permiso deshabilitado.

La representación de los permisos más comunes sería:

- '- - -': Sin permisos para nadie.
- r--: Solamente lectura.
- r-x: Lectura y ejecución.
- rw-: Lectura y escritura.
- rwx: Lectura, escritura y ejecución.

# Configuración de permisos con chmod 
Como hemos dicho al inicio de esta entrada, en sistemas Linux el comando para gestionar los diferentes permisos de un directorio o archivo es **chmod**. Este comando se puede gestionar de dos maneras, la primera es estableciendo los permisos mediante los caracteres rwx y el otro método (que es el más usado) es mediante numeración decimal.

El primer método es muy fácil de entender, primero de todo hay que indicarle al comando qué **tipo de bloque queremos cambiar** el comando, es decir, usuario propietario, grupo y otros usuarios. Usaremos los siguientes caracteres para gestionarlo:
- u: Usuario propietario
- g: Grupo de usuarios
- O: Otros usuarios
- a: Todos

Seguidamente, usaremos los signos **+ o - para añadir o eliminar permisos**, seguido del tipo de permiso que queramos establecer, es decir, "r" para lectura, "w" para escritura y "x" para ejecución.

Vamos a verlo con un ejemplo práctico, vamos a imaginarnos que tenemos un archivo que se llama prueba.txt y queremos establecerle permisos. En el primer ejemplo daremos permisos de escritura al usuario propietario.
>chmod u+w prueba.txt

En el segundo ejemplo, daremos permiso de lectura y escritura al grupo de usuarios, dejando como está el resto de permisos.
>chmod g+rw prueba.txt

Y finalmente, daremos permiso de ejecución al resto de usuarios.
>chmod O+x prueba.txt

Para eliminar permisos el procedimiento es el mismo, pero usaremos el signo "-" en lugar del signo "+".

# Usando el método numérico en chmod
Este método es el más usado, ya que cuando dominamos su aplicación es muy **rápido de implementar**, pero también es el más difícil de entender, puesto que usa **código binario** para establecer los permisos. 

Como hemos dicho, este método usa el código binario para establecer los permisos, es decir, si tenemos los permisos rw-r----x en forma decimal sería 641, ya que en binario sería 110 100 001. A continuación, os comparto la tabla con los diferentes permisos en decimal y binario:

| Permiso | Binario | Decimal |
|---------|---------|---------|
| - - -   | 000     | 0       |
| - - x   | 001     | 1       |
| - w -   | 010     | 2       |
| - w x   | 011     | 3       |
| r - -   | 100     | 4       |
| r - x   | 101     | 5       |
| r w -   | 110     | 6       |
| r w x   | 111     | 7       |



Chmod usa el **sistema decimal** para poder implementar los diferentes permisos, pero por debajo en realidad está usando el **sistema binario** para esta implantación, ya que sería bastante más complicado usar el sistema binario para poder gestionar los permisos.

A continuación, veremos unos ejemplos de gestión de permisos mediante el método numérico:
- En el primer ejemplo queremos establecer permisos de lectura y ejecución al usuario propietario, lectura al grupo de usuarios y sin permiso al resto.

>chmod 640 prueba.txt

- En el segundo ejemplo queremos establecer permisos de lectura, escritura y ejecución para el usuario propietario, lectura y escritura para el grupo de usuarios y lectura para el resto de usuarios.

>chmod 764 prueba.txt

Esta es la teoría de los permisos de Linux con el método numérico, pero me diréis, oye esto es muy complicado de recordar, prefiero la primera opción. Hay una **manera muy sencilla de recordar** que equivale cada número a cada permiso. Os lo muestro seguidamente con un ejemplo práctico.

Sabemos mediante la tabla anterior que el permiso **r-- es un 4**, que el permiso **-w- es un 2** y finalmente que el permiso **--x es un 1**. Sabiendo todo esto simplemente nos queda **sumar**, es decir, si queremos implementar el permiso "rw" pues será 4+2=6, si queremos implementar el permiso r-x será 4+1=5, si queremos implementar el permiso -wx será 2+1=3, y si finalmente queremos establecer el permiso rwx será 4+2+1=7.

Espero que esta entrada en el blog os ayude, por un lado, a despejar dudas cuando intentéis establecer permisos en vuestros sistemas Linux y, por otro lado, que hayáis entendido la teoría que hay detrás, así como el pequeño tip que os he dado para ayudaros a implementarlos mediante el método numérico de chmod.

Para acabar, como siempre, muchas gracias por leer y compartir si os ha gustado. Recordar que me podéis seguir en las redes sociales para más información:
- [Twitter](https://twitter.com/mesteve92)
- [LinkedIn](https://www.linkedin.com/in/meest/)
- [Discord](https://discord.gg/zqvay8T9aE)
