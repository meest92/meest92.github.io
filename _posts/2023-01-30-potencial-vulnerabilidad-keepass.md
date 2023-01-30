---
title: 'Potencial vulnerabilidad en Keepass'
layout: post
post-image: https://cdn.pixabay.com/photo/2016/12/21/17/39/cyber-security-1923446_960_720.png
description: En este post veremos la última vulnerabilidad conocida del gestor de contraseñas mas utilizado, Keepass. Veremos en que consiste esta vulnerabilidad y como podemos mitigarla en nuestro sistema hasta que el desarrolador publique una nueva actualización resolviendo el problema. 
date: 2023-01-30
tags: 
- informacion 
- noticias 
- keepass
- CVE-2023-24055
- vulnerabilidad
- ciberseguridad
---

Si eres usuario de **Keepass**, seguramente has oído que hace unos días se encontró una potencial vulnerabilidad de este popular administrador de contraseñas. Esta vulnerabilidad ha sido catalogada como **CVE-2023-24055** y permitiría a los atacantes obtener las **contraseñas** almacenadas en texto plano, es decir, en texto no cifrado.

# ¿Qué es Keepass?
Keepass es un software open-source diseñado para ser un baúl de contraseñas, es decir, aquí podemos **guardar todas nuestras contraseñas** bajo una clave maestra, de modo que solo nos tendremos que acordar de esta clave maestra para poder acceder al baúl y poder usar las diferentes contraseñas de los servicios que tenemos almacenados en él. La misión principal es ayudarnos a **administrar nuestras contraseñas de manera segura**, ya que es capaz de guardarlas de manera **encriptada** con diferentes tipos de encriptación (AES-256, ChaCha20 y Twofish).

Aparte de ser un baúl de contraseñas, también tiene **diferentes herramientas muy útiles** para poder, por ejemplo, crear contraseñas seguras, establecer caducidad de las contraseñas para ir cambiándolas cada tiempo que se le establezca, entre otras cosas. También existen muchísimos **plugins** que se pueden instalar en la versión base, desarrollados por la comunidad, para extender las funcionalidades base que tiene el software.

# ¿Qué es la vulnerabilidad CVE-2023-24055?

CVE es un proyecto de seguridad centrado en software, financiado por la División de Seguridad Nacional de EEUU y mantenido por MITRE. Al fin y al cabo, CVE es un **glosario para recopilar información sobre vulnerabilidades de seguridad** del software existente. Este glosario es de **dominio público** y está destinado a servir como una línea de base de la industria para poder comunicar las vulnerabilidades que puede haber en un software.

La vulnerabilidad CVE-2023-24055 es una potencial vulnerabilidad que se ha encontrado en Keepass, donde **el atacante es capaz de obtener las contraseñas almacenadas en el baúl sin necesidad de saber la clave maestra y en texto plano**. Esta vulnerabilidad explota el archivo de configuración de Keepass para poder obtener las contraseñas en texto no cifrado, **agregando un disparador de exportación**, siempre que el atacante tenga permisos de escritura sobre el archivo de configuración que está en formato XML.

Una vez el atacante ha introducido el disparador dentro del archivo de configuración, si el usuario abre Keepass como actividad normal y guarda los cambios, el disparador se ejecutará en segundo plano extrayendo las credenciales almacenadas en el baúl en texto plano.

# ¿Qué recomendaciones seguir para mitigarla?
La recomendación actual es desactivar la opción de Exportación dentro de la configuración del programa. Para ello, deberemos ir a **Herramientas > Opciones > Políticas > Desactivar la casilla Exportar**. De esta manera no se podrán exportar las credenciales ni mediante un exploit ni el usuario de Keepass.

De momento, solamente podemos hacer esto hasta que el desarrollador publique una nueva **actualización** del programa para poder tapar esta agujero de seguridad que se ha encontrado.

Para acabar, solamente recordaros que me podéis seguir en las redes sociales para estar informados de todas las novedades.

¡Un abrazo!
