---
title: Diferencia entre Tagged y Untagged ports en una VLAN.
layout: post
post-image: "https://cdn.pixabay.com/photo/2015/05/25/05/27/network-782707_960_720.png"
description: En este post veremos la diferencia entre los distintos metodos que podemos configurar un puerto en una VLAN.
tags:
- VLAN
- networking
- switch
- firewall
- sysadmin
---

Muy buenas a tod@s, hoy os traigo una entrada que hace poco que he podido acabar de entender perfectamente el concepto y ponerlo en práctica. Se trata del concepto de VLAN y en concreto la diferencia entre los puertos Tagged y Untagged.
Pero antes de todo vamos a entender que es eso de VLAN, pues bien, una VLAN es una red de área local de carácter virtual, de hecho, VLAN es un acrónimo de Virtual LAN.

¿Y qué utilidad tiene? Las VLAN nos permiten separar las redes que tengamos en nuestra organización, es decir que podemos hacer tantas subredes como queramos, lo que nos proporcionara la posibilidad de reducir la transmisión del tráfico de la red y mejorar la seguridad, ya que como hemos dicho antes, nos permite encapsular diferentes redes.
Para entenderlo mejor, es como si tuvieses diferentes switchs en una red local configurados con redes distintas, es decir, tener un switch con la LAN 192.168.1.0 y otro switch con la 192.168.0.0. La VLAN nos permite tener diferentes redes sin tener que usar diferentes switchs.

Normalmente, una VLAN se puede configurar en un switch que sea gestionable y mediante, por ejemplo, un Firewall podemos ir creando estas VLAN. Cuando configuramos un switchs para que pueda tener VLAN tenemos que considerar que existen 3 tipos de puertos: Tagged, Untagged y Trunk, que vamos a explicar a continuación.

---
### Puerto Trunk

Este tipo de puerto es el más fácil de entender, ya que es el que viene por defecto en cualquier switch, ya sea gestionable o no. El comportamiento de este puerto es dejar pasar todo el tráfico de cualquier VLAN. Normalmente, se usa cuando tenemos diferentes switchs conectados en cascada, de esta manera el tráfico de todas las VLAN fluyen por todos los switchs que tengamos.

### Puerto Untagged

Este comportamiento de puerto nos permite encapsular el tráfico de una VLAN en concreto, es decir, solamente deja fluir el tráfico de la VLAN que nosotros le hemos establecido y del resto de VLAN no deja pasar el tráfico. Esto quiere decir que todo lo que tengamos conectado en ese puerto solamente tendrá acceso a la VLAN que le hemos establecido y no podrá acceder al resto de VLAN que tengamos configurados en nuestro switch.

### Puerto Tagged

Este comportamiento del puerto nos permite encapsular el tráfico de diferentes VLAN que tengamos configuradas, es decir, deja fluir el tráfico de diferentes VLAN que tengamos configuradas. Un ejemplo practicó seria, si tenemos configuradas las VLAN 100, 101 y 102 y en un puerto tenemos que el comportamiento sea Tagged hacia las VLAN 100 y 102, este dejara pasar el tráfico procedente de la VLAN 100 i 102, pero no dejara pasar el tráfico de la 101, ya que solamente el comportamiento del puerto le permite dejar fluir este tráfico de red.

---
Para acabar, solamente quería dejar claro que si detrás del switch no existe ningún tipo de aparato que gestione el tráfico de VLAN, esto no funcionara. Normalmente, nuestro Firewall de red gestiona el tráfico y es el encargado de redirigir los paquetes hacia la VLAN correcta.


Bueno, espero que esta entrada os sea de utilidad y nos vemos en la siguiente.
¡Como siempre me podéis seguir en redes sociales para más información!


