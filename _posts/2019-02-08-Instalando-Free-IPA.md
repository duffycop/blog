---
title:  Instalando FreeIPA
layout: article
key: 20190208
author: Tino
aside:
  toc: true
---

Hey! Que bueno verte de nuevo! Te hago una pregunta, sabés lo que es un FreeIPA? Si no lo sabés no importa, acá te lo cuento y te digo cómo instalarlo! Mirá.

## Que es FreeIPA (Free Identity Policy Audit)?

Es un proyecto de suite de software de código fuente libre mantenida por el Proyecto Fedora, patrocinada por RedHat, el nombre significa Identidad Políticas Auditoría Libre (en inglés, Free Identity Policy Audit), tiene por objetivo proveer un interfaz segura y sencilla para la administración de identidades comparable a Novell Identity Manager, IBM Security Directory Suite, Redhat 389 Directory Server, Apache Directory Studio y Microsoft Active Directory.<!--more--> [Wikipedia](https://es.wikipedia.org/wiki/FreeIPA)  

## Para que queremos un FreeIPA?  

El FreeIPA nos sirve para manejar los usuarios de nuestra red de forma centralizada.
Seguramente habrás escuchado en algún momento de Active Directory o LDAP o Dominio a nivel de redes empresariales.
Pues lo que sucede es sencillo, en una red hogareña con dos o tres equipos es fácil administrar usuarios y permisos individuales en cada equipo ya que es una cantidad reducida, pero en una red con 50 equipos manejar usuarios y permisos equipo por equipo ya se vuelve algo tedioso, para esto se configura un servidor de Directorio Activo, se manejan todos los usuarios y grupos de forma centralizada en nuestro server de LDAP, Active Directory o FreeIPA. De esta forma agregar un usuario que puedan autenticarse en cualquier equipo, o darle permisos a un usuario para conectarse a determinado recurso compartido o impresora es mucho mas sencillo y puede hacerse desde un equipo y para toda la red.
Ademas podemos conectar otros tipos de servicios para que se autentiquen contra nuestro FreeIPA y que solo usuarios de nuestro dominio puedan acceder a estos servicios, pero esto ya lo veremos más adelante.   

## Que necesitamos?

Necesitamos un equipo con CentOS 7 en este caso sera virtualizado y la tarjeta de red estará en modo NAT.  

>Ethernet: ens33  
>IP: 172.16.121.136  
>Hostname: ipa.tino.uy

## Configuración previa
1. Para esto vamos a configurar el CentOS 7, nos conectamos por ssh  
~~~ bash
ssh root@172.16.121.136
~~~
2. Y borramos el contenido del archivo _/etc/hostname_ y colocamos el nombre fqdn que querramos para nuestro LDAP  
~~~
ipa.tino.uy
~~~
3. Luego editamos el archivo _/etc/hosts/_, lo mismo que antes  
~~~
172.16.121.136 ipa.tino.uy ipa.tino.uy ipa
~~~
4. Configuración de firewall  
~~~ bash
firewall-cmd --permanent --add-port={80/tcp,443/tcp,389/tcp,636/tcp,88/tcp,464/tcp,53/tcp,88/udp,464/udp,53/udp,123/udp}
firewall-cmd --reload
~~~
5. Luego reiniciamos el centos con _reboot_ y volvemos a conectarnos por ssh (ver paso 1)  

## Configurando el Generador de Números Aleatorios

Configurar un FreeIPA requiere de un montón de datos aleatorios para las operaciones criptográficas que corre. Por defecto una maquina virtual se queda sin datos aleatorios o entropía muy rápidamente. Para solucionar esto usaremos ***rngd***, un software generador de números aleatorios.  

1. Debemos instalar ***rngd***  
~~~ bash
yum install rng-tools
systemctl start rngd
systemctl enable rngd
systemctl status rngd
~~~
La salida debe incluir active (running) en verde.  

## Instalando FreeIPA

1. Ahora si, instalamos el paquete ipa-server.  
~~~ bash
yum install ipa-server
~~~

2. Ejecutar el comando de instalación.  
~~~ bash
ipa-server-install
~~~

3. Prompt de la instalación.  
~~~
Do you want to configure integrated DNS (BIND)? [no]: no  
Server host name [ipa.tino.uy]: ipa.tino.uy  
Please confirm the domain name [tino.uy]: ipa.tino.uy  
Please provide a realm name [IPA.TINO.UY]: ipa.tino.uy  
Directory Manager password:  
IPA admin password:  
Continue to configure the system with these values? [no]: yes  
~~~
El proceso de instalación llevará unos minutos.  

### Verificar las funciones de FreeIPA

1. Primero debemos verificar que el dominio de Kerberos fue instalado correctamente intentando inicializar el token de Kerberos para el usuario admin.  
~~~ bash
kinit admin
~~~
Si funciona correctamente entonces debería pedirte la contraseña de admin de FreeIPA que escribimos hace unos minutos.  

2. Verificar que el servidor IPA funciona adecuadamente.  
~~~ bash
ipa user-find admin
~~~
La salida de este comando debería ser la siguiente.  
~~~
---------------------
1 usuario coincidente
---------------------
  Ingreso de usuario: admin
  Apellido: Administrator
  Directorio principal: /home/admin
  Shell de ingreso: /bin/bash
  Principal alias: admin@IPA.TINO.UY
  UID: 836200000
  GID: 836200000
  Cuenta inhabilitada : False
--------------------------------
Cantidad de entradas devueltas 1
--------------------------------
~~~
También tendremos acceso a una interfaz web en https://ipa.tino.uy

Y voilá, tenemos FreeIPA instalado y listo para usar.
