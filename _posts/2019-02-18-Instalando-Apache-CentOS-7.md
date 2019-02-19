---
title:  Instalando Apache en CentOS 7
key: 20190218
author: Tino
---
Hey! Me alegra verte de nuevo por aquí!
Hoy vamos a ver la instalación de Apache Web Server en un CentOS 7.

### Que es Apache?
El servidor HTTP Apache es un servidor web HTTP de código abierto, para plataformas Unix, Microsoft Windows, Macintosh y otras, que implementa el protocolo HTTP/1.1 y la noción de sitio virtual según la normativa RFC 2616. [Wikipedia](https://es.wikipedia.org/wiki/Servidor_HTTP_Apache) <!-- more -->  

### Que necesitamos?

Un equipo con CentOS instalado y conexión a internet.

### Updates

1. Lo primero es comprobar que nuestro equipo esta up-to-date.
```
yum update
```
Una vez actualizado todo procedemos a instalar.

### Instalando Apache

1. Ahora ejecutamos el comando de instalación de apache.
```
yum install httpd
```
2. Nos preguntara si queremos instalar httpd y sus dependencias
```code
Tamaño total de la descarga: 3.0 M
Tamaño instalado: 10 M
Is this ok [y/d/N]: y
```
Esto comenzara con la instalación de Apache y la salida deberá ser similar a esto.
```code
apr-util.x86_64 0:1.5.2-6.el7
httpd-tools.x86_64 0:2.4.6-88.el7.centos
mailcap.noarch 0:2.1.41-2.el7
¡Listo!
```
3. Iniciamos el servicio
```
systemctl start httpd
```
Debemos recordar que el daemon de Apache en las distribuciones RHEL como Fedora/CentOS/Red Hat se llama httpd y no apache2 como en las distros Debian como Debian/Ubuntu/Canaima.
Si queremos que Apache se inicie con el sistema debemos habilitar el daemon.
```
systemctl enable httpd
```  
4. Y no menos importante, habilitar el puerto 80 en el firewall, para esto haremos lo siguiente:
```
firewall-cmd --zone=public --permanent --add-port=80/tcp
firewall-cmd --reload
```

Y voilá tenemos Apache instalado, ahora si queremos agregar sitios web, usarlo como reverse proxy u otras cosas debemos configurarlo correctamente.
