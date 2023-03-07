---
title: 'Implementación de LAPS'
layout: post
post-image: https://cdn.pixabay.com/photo/2016/05/27/05/40/computer-network-1419136_960_720.png
description: En este post veremos cómo implementar LAPS en un sistema Windows Server 2022 con clientes Windows 10.
date: 2023-03-07
tags: 
- sysadmin 
- gpo 
- windows
- server
- password
- localadmin
---

Microsoft pone a disposición de todo administrador de sistemas una herramienta llamada LAPS, que nos permite establecer una contraseña para el usuario local de nuestras máquinas clientes. En esta entrada veremos cómo funciona LAPS y cómo implementarlo en nuestras infraestructuras cliente-servidor mediante un Windows Server 2022 y Windows 10.

# ¿Qué es LAPS?

**Local Administrator Password Solution (LAPS)** es una característica de Windows que nos permite **administrar las contraseñas** de los administradores locales de nuestro sistema. Esta herramienta almacena de forma **centralizada** en nuestro Active Directory las contraseñas de los usuarios administradores de nuestros equipos. 

Esta herramienta va más allá, ya que nos permite establecer **contraseñas randomizadas** a nuestros usuarios administradores y **establecer una política de caducidad a estas contraseñas**. LAPS renovará esta contraseña de manera automática una vez haya llegado al máximo de caducidad de la contraseña, permitiéndonos de esa manera mejorar la seguridad de nuestros sistemas.

Esta herramienta solamente puede ser implementada en nuestros sistemas si cumplimos con los siguientes requisitos:
- Tener habilitado el rol de Active Directory.
- Tener .NET Framework 4.0
- Powershell 2.0
- Es soportado por todas las versiones de Windows Server y Windows Desktop.

# ¿Cómo instalar LAPS?

Primero de todo, descargaremos la herramienta desde el sitio web de Microsoft. Os dejo el [enlace](https://www.microsoft.com/en-us/download/details.aspx?id=46899) para poder descargar.

Seguidamente, realizaremos la instalación de la herramienta en nuestro **servidor de Active Directory**, simplemente ejecutamos el paquete msi que hemos descargado y realizaremos la instalación de la función **"AdmPwd GPO Extension"** que es la que nos permitirá administrar todos los equipos del dominio.

![Instalación-LAPS](/assets/images/2023-03-07/img1.png)

Una vez todo instalado, podremos crear diferentes grupos dentro de la estructura de Active Directory, es decir, podremos gestionar qué usuarios o grupos de usuarios tienen permiso de lectura y escritura sobre las máquinas del dominio. En nuestro caso, crearemos tres grupos diferentes dentro de una OU de nuestro Active Directory:
- LAPS R: Solamente podrá leer.
- LAPS RW: Permite la escritura.
- LAPS SERVER: Nos permitirá gestionar los servidores.

Una vez creados los diferentes grupos, podremos incorporar usuarios o grupos de usuarios dentro de ellos para poder administrar correctamente. En nuestro caso, usaremos dos usuarios, MeestR y MeestRW. En el grupo de **LAPS R** añadiremos ambos:

![Grupo-Lectura](/assets/images/2023-03-07/img2.png)

Y en el grupo de **LAPS RW** añadiremos solamente el usuario MeestRW, ya que este usuario será el que podrá realizar modificaciones.

![Grupo-Escritura](/assets/images/2023-03-07/img3.png)

El siguiente paso que debemos realizar, consiste en **extender el esquema del servidor**, puesto que debemos permitir que estas funcionalidades de LAPS estén optimizadas con el dominio. Para poder realizarlo usaremos el siguiente comando de PowerShell:

````powershell
Update-AdmPwdADSchema
````

![Powershell1](/assets/images/2023-03-07/img4.png)

>NOTA: Puede ser que este comando os devuelva algún error en el momento de ser ejecutado, si es así deberemos ejecutar previamente este comando:
````powershell
Import-Module AdwPwd.PS
````

Finalmente, deberemos **dar permisos a los usuarios** que tenemos en la OU para que puedan actualizar su contraseña en el Active Directory, para ello, usaremos el siguiente comando:

````powershell
Set-AdmPwdComputerSelfPermission -OrgUnit (OU_name)
````

![Powershell2](/assets/images/2023-03-07/img5.png)

Y también deberemos **dar permisos a los diferentes grupos** que hemos creado, uno de lectura y el otro de escritura, para ello usaremos los siguientes comandos respectivamente:
````powershell
Set-AdmPwdReadPasswordPermission -OrgUnit (OU_name) -AllowedPrincipals "group"
````

![Powershell3](/assets/images/2023-03-07/img6.png)

````powershell
Set-AdmPwdResetPasswordPermission -OrgUnit (OU_name) -AllowedPrincipals "group"
````

![Powershell4](/assets/images/2023-03-07/img7.png)

# ¿Cómo instalar LAPS en los equipos clientes mediante GPO?

Una vez realizada la instalación de LAPS en el servidor, ya podremos realizar la instalación en nuestros equipos del dominio, para ello configuraremos desde el servidor una **nueva política de grupo (GPO)** capaz de instalar el paquete msi que hemos instalado anteriormente y de implementar la política necesaria en los equipos para que puedan usar la herramienta. 

Primero, usaremos la utilidad **Administración de directivas de grupo**, allí crearemos una nueva política con el nombre que más nos convenga. Editamos la política y navegamos hacia la ruta **Configuración del equipo > Directivas > Configuración de software**. Haremos clic derecho sobre la opción **Instalación de software** y seleccionaremos la opción de **Nuevo > Paquete**.

![DirectivaGPO1](/assets/images/2023-03-07/img8.png)

Buscaremos el instalador de LAPS, que deberá estar alojado en alguna **carpeta que los usuarios del dominio tengan acceso**, o incluso en la ruta Sysvol.

![DirectivaGPO2](/assets/images/2023-03-07/img9.png)

Por otro lado, deberemos **activar algunas funcionalidades** mediante la GPO que hemos creado, para ellos nos iremos dentro de la configuración de la GPO, a la ruta **Configuración del equipo > Directivas > Plantillas Administrativas > LAPS** y habilitaremos la política **Habilitar la administración de contraseñas de administrador local**.

![DirectivasGPO3](/assets/images/2023-03-07/img10.png)

Aparte de esta política, también deberemos configurar la que se llama **Configuración de contraseña**, donde gestionaremos de qué forma se deberá asignar la contraseña a los equipos del dominio. Aquí podremos configurar:

- La complejidad de la contraseña.
- La longitud de la contraseña.
- Y la caducidad de la contraseña.

Todos estos parámetros los dejaremos configurados a nuestro gusto.

![DirectivasGPO4](/assets/images/2023-03-07/img11.png)

Para finalizar, con la política **Nombre de la cuenta de administrador que se va a administrar** podremos definir el nombre del usuario administrador que controlara estas contraseñas, lo único que hay que tener en cuenta es que **no puede ser el usuario administrador** que viene por defecto.

Para finalizar, con la configuración de la GPO, hacemos clic derecho sobre la OU en el panel principal del Administrador de política de grupo para poder enlazar esta política con la OU donde están los usuarios que queremos administrar mediante LAPS y usaremos el siguiente comando para actualizar las políticas de grupo en el servidor:

````cmd
gpupdate /force
````

# ¿Cómo consultar la contraseña del usuario administrador?

Una vez todo configurado y comprobado que el ordenador cliente ha actualizado correctamente las políticas de grupo, ya podremos acceder a nuestro Active Directory, iremos a buscar el **equipo** que queramos consultar la contraseña del usuario administrador, haremos clic derecho y clicaremos en la opción de propiedades, aquí dentro tendremos una pestaña llamada **Editor de Atributos** y es aquí dentro donde existe un atributo llamado **ms-Mcs-AdmPwd** donde veremos la contraseña del usuario administrador de este equipo.

![AD-check](/assets/images/2023-03-07/img12.png)

LAPS también tiene una **aplicación de escritorio**, que se puede instalar con el paquete msi de LAPS, donde podremos buscar el nombre del equipo sobre el cual necesitamos consultar la contraseña del usuario administrador y nos la mostrará para que la podamos usar para realizar cualquier acción que se requiera el usuario administrador local del equipo.

![LAPS-App](/assets/images/2023-03-07/img13.png)

Para acabar, como siempre, muchas gracias por leer y compartir si os ha gustado. Recordar que me podéis seguir en las redes sociales para más información:
- [Twitter](https://twitter.com/mesteve92)
- [LinkedIn](https://www.linkedin.com/in/meest/)
- [Discord](https://discord.gg/zqvay8T9aE)
