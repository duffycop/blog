---
layout: article
title:  JumpServer
key: 20200706
author: Tino
aside:
  toc: true
---

Mi3rd4! Un usuario accedió al server y rompió todo.

Ehhh no, estas cosas no pueden pasar. Los usuarios no deben tener acceso a la red de los servidores, para esto se segmentan las redes.<!--more--> Todos los usuarios por un lado, todos los servers por otro y no solo eso, sino que cada ambiente también debería ir segmentado, Producción, Preproducción, Testing, Desarrollo., todos deberían ir en redes (vlans, o cualquier tipo de segmentación) diferentes.
Y no deberíamos de tener equipos de la red de usuarios conectados a las redes de los servidores, toda tarea que se ejecute sobre los servidores debería hacerse a través de un jumpserver o también conocido como bastion host, en el cual monitoreamos toda la actividad de nuestros usuarios/administradores sobre los servidores.

Sé que te prometí un post de WAF, pero todavía no lo he armado, en cambio te traigo este bonito post sobre Apache Guacamole! Probalo y contame qué te pareció Guacamole y el post.

## Qué es un JumpServer?
[Wikipedia](https://en.wikipedia.org/wiki/Jump_server) nos dice que es un ordenador en una red utilizada para acceder y administrar dispositivos en una zona de seguridad por separado. El ejemplo más común es la gestión de un host en una zona desmilitarizada de redes o equipos de confianza. Nosotros no vamos a usar una DMZ vamos a segmentar nuestras redes internas.

## Empecemos
### Que necesitamos?
Para este ejemplo solo vamos a tener 2 subredes, supongamos que tenemos a los usuarios en la red A y los servers en la red B, las subredes en este ejemplo son random.

La red A contiene las workstations:
~~~ bash
192.168.0.0/24
  workstation sysadmin - 192.168.0.100
  workstation 1 - 192.168.0.101
  workstation 2 - 192.168.0.102
  workstation 3 - 192.168.0.103
~~~

La red B contiene los servidores:
~~~ bash
10.10.0.0/16
  jumpserver - 10.10.1.10
  server 2 - 10.10.1.11
  server 3 - 10.10.1.12
~~~

Las configuraciones de firewall y segmentación no están dentro del alcance de este post. Con lo cual vamos a suponer que ya tenemos configurada esta parte.
Asumimos que la red B está completamente desconectada de la red A y que el jumpserver tiene una interfaz dentro de la red de servidores y una interfaz dentro de la red interna.
De esta forma si queremos acceder a un equipo de la red de servidores sólo podrá hacerse desde nuestro jumpserver.

Ahora, todo bonito así contado, pero cuando vamos al "hands-on" qué herramientas vamos a usar?
Y en este caso lo que vamos a usar es nuestra distro amiga CentOS 7 y Apache Guacamole para hacer de jumpserver.

### ¿Por qué Guacamole?
1. Licencia Apache
2. Gratuito
3. Permite grabar todo lo que hacemos en los servidores
4. ¿Por qué no?

Entonces, para esto tenemos un CentOS 7 nuevo de paquete.
Al cual le pusimos la IP 10.10.1.10 dentro de la red de servidores. En nuestro firewall ya configuramos que bloquee todas las conexiones a los equipos de la red de servidores, excepto el puerto 8080 (o el 80*) y el 22 momentaneamente solamente desde la IP 192.168.0.100 (sysadmin) hasta poder conectarnos con Guacamole a nuestro JumpServer.
Si tenemos otros servicios que necesitemos tener abiertos a nuestros usuarios, una intranet, un puerto en particular para una aplicación desktop, etc. Debe configurarse en el firewall también. Es necesario tener en cuenta que lo que queremos controlar es la conexión remota a estos equipos y el uso interactivo de los mismos.
No necesariamente necesitamos bloquear los accesos de aplicaciones y otras yerbas.

### Instalación
Bien, hacemos SSH a root@10.10.1.10.
Acá comienza lo jugoso:

~~~ bash
yum update && yum upgrade
wget https://raw.githubusercontent.com/Zer0CoolX/guacamole-install-rhel/master/guac-install.sh
chmod +x guac-install.sh
./guac-install.sh
~~~

Primero que nada agradecer a [Zer0CoolX](https://github.com/Zer0CoolX) por su fantabuloso script automágico. El proceso de instalación de Guacamole puede ser bastante tedioso, pero este señor nos lo hizo simple.
Después de seguir el proceso de instalación del script tendremos un NGINX (que a mí particularmente no me funcionó y mucho menos como yo esperaba pero no nos importa, tenemos un [Apache como Reverse Proxy]({% post_url 2020-01-10-Apache-Proxy-Inverso %}) el cual podremos configurar para apuntar al Guacamole con un subdominio más bonito), también tendremos instalado un Tomcat y el daemon de Guacamole (guacd).
¿Cómo sigue esto? Pues bien, vamos a http://10.10.1.10:8080/guacamole y accedemos con las credenciales por defecto que son:
~~~
usuario: guacadmin
password: guacadmin
~~~

Y listo estamos dentro de Guacamole, aquí podremos configurar usuarios, accesos y otras yerbas.

### Grabaciones
Para la grabación de las sesiones tuve que googlear un poquito (gracias [StackOverflow](https://stackoverflow.com/questions/60630083/how-to-setup-guacamole-session-recording-with-rdp-and-ssh), pero no fué muy complicado de arreglar.
Creé un directorio con permisos para el daemon guacd, en mi caso lo puse en /var/tmp/guacamole
~~~ bash
# ls -l | grep guacamole
drwxr-xr-x. 2 guacd guacd 162 jul 10 14:46 guacamole
~~~
Entonces al configurar la conexión puse lo siguiente:
~~~
Ruta de grabación: /var/tmp/guacamole/HOST
Nombre de grabación: ${GUAC_USERNAME}_${GUAC_DATE}_${GUAC_TIME}
~~~
Donde HOST es el nombre del host al que quiero conectarme, entonces me queda en el path /var/tmp/guacamole todas las grabaciones separadas en cada carpeta de host y con nombres descriptivos como el siguiente: tino_20200710_144616.
Las grabaciones quedan en un formato que solamente es interpretable por Guacamole, peeeero, la ventaja es que queda bastante comprimido, en una prueba realizada a un RDP durante 43 minutos pesaba 9MB.
Para transformarlas a un formato de audio hay que usar un comando como el siguiente:
> guacenc -s 1280x720 -r 20000000 -f tino_20200710_144616

Esto nos crea un archivo tino_20200710_144616.m4v y podemos verlo en nuestro reproductor favorito.


Espero volver a verte pronto! Saludos!
