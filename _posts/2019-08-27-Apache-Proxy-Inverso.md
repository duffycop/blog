---
title:  Apache como Proxy Inverso
key: 20190827
author: Tino
---
Hola! Que bueno verte otra vez por aquí!
Vamos a aprender a utilizar Apache como Proxy Inverso o Proxy Reverso.

### Que es Apache?
El servidor HTTP Apache es un servidor web HTTP de código abierto, para plataformas Unix, Microsoft Windows, Macintosh y otras, que implementa el protocolo HTTP/1.1 y la noción de sitio virtual según la normativa RFC 2616. [Wikipedia](https://es.wikipedia.org/wiki/Servidor_HTTP_Apache) <!-- more -->  

### Que necesitamos?

Un equipo con CentOS instalado y Apache instalado.

### Updates

1. Primero debemos tener en cuenta algunos comandos.
```
systemctl start httpd
systemctl stop httpd
systemctl enable httpd
systemctl status httpd
httpd -t
httpd -M
httpd -k graceful
setenforce 0
```
Sea que lo tengamos iniciado o no eso no importa, ya iremos probando la nueva configuración a medida que la vamos cambiando.
Expliquemos un poco estos comandos.

* *_systemctl start httpd_* este comando es utilizado para iniciar el Apache
* *_systemctl stop httpd_* con este en cambio lo detenemos
* *_systemctl enable httpd_* el parámetro enable hará que Apache inicie automáticamente con el sistema
* *_systemctl status httpd_* status nos mostrará el estado del demonio
* *_httpd -t_* este comando va a revisar la sintaxis de la configuración y nos dirá si tenemos algún error en la misma
* *_httpd -M_* con este parámetro veremos los módulos que tenemos agregados y habilitados en Apache
* *_httpd -k graceful_* con este comando reiniciamos el servicio sin matar ninguna sesión activa
* *_setenforce 0_* con esto vamos a deshabilitar SELinux, más adelante dedicaré un post completo a SELinux explicando funciones y administración básica del mismo, pero por el momento lo deshabilitaremos.

### Directorios de Apache

1. Los directorios más importantes de Apache.
```code
/var/log/httpd
/etc/httpd
/etc/httpd/conf
/etc/httpd/conf.d
```
{% comment %} 
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
{% endcomment %}