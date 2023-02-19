---
title: 'Cómo configurar el fondo de pantalla por GPO'
layout: post
post-image: https://cdn.pixabay.com/photo/2020/04/04/04/23/folder-5000783_960_720.png
description: En este post veremos cómo configurar un fondo de pantalla mediante políticas de grupo de un Windows Server 2022 y una estación cliente en Windows 10. 
date: 2023-02-19
tags: 
- sysadmin 
- gpo 
- windows
- server
- wallpaper
- fondo
---

Como administrador de servidores me he encontrado con muchos clientes que quieren tener un **fondo de pantalla corporativo** en sus equipos, el problema viene cuando este cliente tiene muchas estaciones de trabajo y se hace muy pesado configurar los fondos de pantalla de manera manual. En esta entrada veremos cómo configurar en nuestro servidor Windows Server las políticas de grupo para que se **autoconfigure el fondo de pantalla** en las diferentes estaciones de trabajo e incluso podremos decidir qué fondo de pantalla establecer dependiendo de los departamentos de la empresa.

# ¿Qué es una polítca de grupo?
Empezamos por el principio, una **política de grupo de Windows** es una directiva que se establece en servidores Windows que nos permite realizar una gran variedad de configuraciones avanzadas dentro de un entorno de trabajo Windows, es decir, que no podremos establecer políticas si usamos como equipos clientes otro sistema operativo que no sea Windows. Mencionar que solamente estas políticas de grupo están diseñadas para usuarios profesionales, es decir, deberemos tener instalado las versiones de Windows Pro, UItimate y Enterprise.

Existen **dos tipos de políticas de grupo**:

### Política de grupo centralizada
Son las diferentes directivas que podemos establecer en un entrono Windows **cliente-servidor**. Es decir, que necesitamos un servidor Windows configurado con **Active Directory** para poder usar estas políticas. Estas directivas son las más usadas en entornos **empresariales**, ya que permiten una gestión centralizada para configurar una gran variedad de configuraciones de Windows.

### Política de grupo local
Estas políticas de grupo existen en todas las distribuciones Windows para empresas (Pro y Enterprise) y nos permiten realizar muchas configuraciones en los equipos de **manera local**, es decir, que deberemos configurar los ordenadores uno por uno con las políticas que nosotros queramos. Este tipo de directivas se usan en entornos donde **carecemos de un servidor centralizado con Active Directory**, permitiendo configurar un sin fin de configuraciones de Windows, pero de manera individualizada, de modo que se hace mucho más pesada esta configuración respecto la de políticas de grupo centralizadas.

# ¿Cómo configurar un fondo de pantalla mediante GPO?
Una vez enumeradas las diferentes politicas de grupo, en nuestro tutorial usaremos un entorno de políticas de grupo centralizado mediante un Windows Server 2022 con el rol de Active Directory y un Windows 10 como equipo cliente.

Para realizar esta configuración, primero, **guardaremos nuestro fondo de pantalla en una ubicación de nuestro servidor que esté compartida con el resto de equipos**. Si no tenemos ninguna ubicación, la podremos generar dando **clic derecho encima de la carpeta que contiene el fondo > Propiedades > Compartir**. Aquí deberemos añadir los grupos de **Usuarios del Dominio y Equipos del Dominio** y darles permisos de lectura solamente.

![Shared-Folder](/assets/images/2023-02-19/img1.png)

Una vez compartida la carpeta, es hora de realizar la configuración de las GPO. Añadiremos una nueva GPO usando la **herramienta de administración de directivas de grupo** en el apartado de **Objetos de política de grupo**. 

![New-GPO](/assets/images/2023-02-19/img2.png)

Después de crear el nuevo objeto lo editamos e iremos a buscar la siguiente opción:

> User Configuration > Preferences > Windows Settings > Files 

Aquí añadiremos un nuevo archivo mediante la acción de **Actualizar** y en los campos **"Campo de origen"** y **"Campo de destino"** escribiremos la ruta absoluta donde tenemos el fondo de pantalla en el servidor y la ruta donde queremos transferir este fondo en los equipos clientes.

![New-File-GPO](/assets/images/2023-02-19/img3.png)

En resumen, esta política nos transferirá a nuestros equipos clientes el fondo de pantalla que le hemos establecido a la ruta deseada.

El siguiente paso es configurar la directiva para que **establezca el fondo de pantalla** que hemos transferido anteriormente en los diferentes equipos. Para ello iremos a:

> User Configuration > Polices > Administrative Templates > Desktop > Desktop

![Desktop-Wallpaper-GPO](/assets/images/2023-02-19/img4.png)

Aquí configuramos la directiva llamada **Fondo de escritorio**, que dejaremos activada y establecemos la ruta donde se encuentra el fondo de pantalla dentro de los equipos clientes y finalmente el estilo del fondo (Expandido, mosaico, Rellenar, etc.).

![Config-Desktop-Wallpaper-GPO](/assets/images/2023-02-19/img5.png)

Finalmente, **asignamos esta política donde queremos que se aplique**, se puede aplicar a todo el dominio o si se requiere una configuración distinta por departamentos, podremos establecer esta política a las diferentes **Unidades Organizativas (OU)** que tengamos creadas en nuestro Active Directory, de esta manera cada departamento podrá tener su propio fondo de pantalla.

![Deploy-GPO](/assets/images/2023-02-19/img6.png)

Ahora, si reiniciamos el ordenador cliente podremos comprobar cómo se genera la carpeta con el fondo de pantalla en la ruta que hemos especificado en la política de grupo y también podremos observar cómo se cambia el fondo de pantalla por el que le hemos establecido.

Para acabar, solamente recordaros que me podéis seguir en las redes sociales para estar informados de todas las novedades.

¡Un abrazo!
