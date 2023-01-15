---
title: Lentitud al navegar por internet usando GlobalVPN
layout: post
post-image: "https://cdn.pixabay.com/photo/2018/04/21/02/11/iot-3337536_960_720.png"
description: En este post veremos como arreglar la lentitud de navegación de Internet usando el adaptador Wi-Fi de nuestro equipo y GlobalVPN.
tags:
- VPN
- networking
- issue
- internet
- navegar
- GlobalVPN
- Sonicwall
- sysadmin
---

¡Muy buenas a tod@s!

En la primera entrada del año os traigo la solución a un problema de lentitud usando el cliente de IPsec VPN de SonicWall, GlobalVPN. Puede ser que os ha pasado que cuando estáis conectados por GlobalVPN y usamos la conexión Wifi de nuestro ordenador, la navegación por internet funciona con extrema lentitud, pues bien, esto se debe a un problema con el componente Receieve Segment Coalescing (RSC).

Y me diréis: ¿qué es eso del RSC? Pues RSC, y según Microsoft, es una tecnología de descarga sin estado que ayuda a reducir la utilización de la CPU para el procesamiento de red en el lado de recepción mediante la descarga de tareas de la CPU a un adaptador de red compatible. En otras palabras, este componente libera la carga de trabajo de la CPU cuando estamos descargando en una red y las manda a nuestro adaptador de red para realizar el procesamiento de la descarga.

Pues parece ser que en Windows 10 y usando GlobalVPN como cliente VPN, este componente ralentiza substancialmente la velocidad de transferencia de nuestro ordenador, provocando una lentitud excesiva cuando intentamos navegar por Internet.

Como todo en la vida tiene solución, son unos pasos muy sencillos que lo que hará es solucionar esta lentitud que ocasiona el componente.

---
### Pasos para desactivar RSC

* Ejecutamos PowerShell como administrador.

* Debemos obtener el nombre de nuestro adaptador de red Wifi, para ello usarmeos el comando Get-NetAdapter.
![
   Get-NetAdapter](/assets/images/2023-01-15/Image_Get-NetAdapter.png)

* Vemos que en nuestro caso, el adaptador de red se llama Wi-FI. Nos copiamos este nombre.

* Comprobamos si RSC está activado en nuestro sistema usando el comando: Get-NetAadapterRsc.

   ![Get-NetAdapterRsc](/assets/images/2023-01-15/Image_Get-NetAdapterRsc.png)

* Si el estado de IPv4Enables es True, procedmos a desactivar RSC usando el comando Disable-NetAdapterRsc -Name Wi-Fi

   ![Disable-NetAdapterRsc](/assets/images/2023-01-15/Image_Disable-NetAdapterRsc.png)

* Comprobamos si el estado de RSC es False usando el mismo comando que antes: Get-NetAdapterRsc.

* Si todo es correcto, veremos que ahora nuestra velocidad ha mejorado substancialmente.

---

Bueno, espero que esta entrada os sea de utilidad para solventar este problema muy molesto. Como siempre, muchas gracias por leerme y acordaos que me podéis seguir en redes sociales para más información.


