---
title:  Apache como Proxy Inverso
layout: article
key: 20200110
author: Tino
aside:
  toc: true
---

Hola! Que bueno verte otra vez por aquí!
Vamos a aprender a utilizar Apache como Proxy Inverso o Proxy Reverso.<!--more-->  

## Que es un Servidor Proxy?
Un proxy, o servidor proxy, en una red informática, es un servidor —programa o dispositivo—, que hace de intermediario en las peticiones de recursos que realiza un cliente (A) a otro servidor (C). Por ejemplo, si una hipotética máquina A solicita un recurso a C, lo hará mediante una petición a B, que a su vez trasladará la petición a C; de esta forma C no sabrá que la petición procedió originalmente de A. [Wikipedia](https://es.wikipedia.org/wiki/Servidor_proxy).
Pero probablemente eres muy curioso y ya conoces como trabaja un proxy, normalmente se utiliza como un intermediario entre un equipo o red e Internet. El proxy inverso funciona exactamente al revés, un equipo cualquiera de Internet quiere acceder a un equipo de nuestra red, entonces pasa por este intermediario y deriva la petición en uno o varios servidores de backend.

## Que necesitamos?

Un equipo CentOS instalado y Apache, si no sabes como instalar Apache puedes verlo en [este post]({% post_url 2019-02-18-Instalando-Apache-CentOS-7 %}).

## Chequeando mod_proxy

1. Lo primero será chequear que mod_proxy esté habilitado y corriendo en Apache, por lo general viene habilitado por defecto.
~~~ bash
httpd -M | grep proxy_module
~~~
La salida debe ser esta:
~~~ bash
proxy_module (shared)
~~~

2. Ya con el módulo activado lo que debemos hacer es configurar nuestro Virtual Host.

## Configurando Reverse Proxy

1. Creamos el archivo de nuestro Virtual Host.
~~~ bash
touch /etc/httpd/conf.d/example.conf
~~~

2. Lo editamos con vi, vim, nano o el editor de nuestra preferencia y agregamos lo siguiente:
{% highlight apache %}
<VirtualHost *:80>

        ServerName example.com
        ServerAlias www.example.com

        ProxyPass "/" "http://10.0.0.1/"
        ProxyPassReverse "/" "http://10.0.0.1/"

</VirtualHost>
{% endhighlight %}

Donde example.com es nuestro dominio y 10.0.0.1 es el server al que queremos enviar el request.
Para guardar en Vi o Vim presionamos Esc, luego escribimos :wq y damos enter.
Para guardar en Nano Ctrl + X y nos pregunta si deseamos guardar, digitamos "Y" y presionamos enter.

### Configurando Load Balancer a multiples backends

1. Debemos chequear que el módulo del loadbalancer está activado.
~~~ bash
httpd -M | grep proxy_balancer_module
~~~
La salida debe ser esta:
~~~ bash
proxy_balancer_module (shared)
~~~

2. Igual que en el apartado anterior creamos el Virtual Host pero esta vez escribimos lo siguiente.
{% highlight apache %}
<VirtualHost *:80>

	ServerName example.com
	ServerAlias www.example.com

	<Proxy balancer://mycluster>
		BalancerMember http://10.0.0.1:8080
		BalancerMember http://10.0.0.2:8080
	</Proxy>

	ProxyPreserveHost On

	ProxyPass / balancer://mycluster/
	ProxyPassReverse / balancer://mycluster/

</VirtualHost>
{% endhighlight %}

De esta forma habremos configurado nuestro Apache como Proxy Reverso, tanto para uno como para múltiples backends.
