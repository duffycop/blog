---
title:  Apache como LoadBalancer
layout: article
key: 20200206
author: Tino
aside:
  toc: true
---

Opa! Ya se te está haciendo costumbre visitarme.
Sabés que hacemos hoy? Hoy vamos a usar Apache como LoadBalancer.<!--more-->

### Qué es un LoadBalancer?
Un balanceador de carga distribuye la carga entre dos o más servidores de backend, de esta forma podemos aumentar la disponibilidad ya que si se cae uno de los servidores backend la aplicación puede seguir funcionando con los demás, además al distribuir la carga disminuye el tiempo de respuesta porque todos los servidores trabajan con menor cantidad de carga lo cual influye en la velocidad de respuesta.

### Que necesitamos?

Un equipo CentOS instalado y Apache, si no sabes como instalar Apache puedes verlo en [este post]({% post_url 2019-02-18-Instalando-Apache-CentOS-7 %}).

### Chequeando mod_proxy_balancer

1. Lo primero será chequear que mod_proxy_balancer esté habilitado y corriendo en Apache, por lo general viene habilitado por defecto.
~~~ bash
httpd -M | grep proxy_balancer_module
~~~
La salida debe ser esta:
~~~ bash
proxy_balancer_module (shared)
~~~

2. Ya con el módulo activado lo que debemos hacer es configurar nuestro Virtual Host.

### Configurando Reverse Proxy

1. Creamos el archivo de nuestro Virtual Host.
~~~ bash
touch /etc/httpd/conf.d/example.conf
~~~

2. Lo editamos con vi, vim, nano o el editor de nuestra preferencia y agregamos lo siguiente:
{% highlight apache %}
<VirtualHost *:80>

        ServerName example.com
        ServerAlias www.example.com

        <Proxy "balancer://mycluster">
            BalancerMember "http://10.0.0.2:80"
            BalancerMember "http://10.0.0.3:80"
            BalancerMember "http://10.0.0.4:80"
            BalancerMember "http://10.0.0.5:80"
        </Proxy>

        ProxyPass        "/test" "balancer://mycluster"
        ProxyPassReverse "/test" "balancer://mycluster"

</VirtualHost>
{% endhighlight %}

Donde example.com es nuestro dominio y 10.0.0.2-5 son los servers que queremos balancear.
Para guardar en Vi o Vim presionamos Esc, luego escribimos :wq y damos enter.
Para guardar en Nano Ctrl + X y nos pregunta si deseamos guardar, digitamos "Y" y presionamos enter.

Listo, cuando vamos a example.com el loadbalancer nos va a distribuir la carga entre todos los miembros del "cluster".
A partir de ahora si apagamos el 10.0.0.4 por mantenimiento, actualizaciones, etc, podremos seguir atentiendo los requests.

Actualmente no tenemos un balanceo de carga teniendo en cuenta la salud, esto quiere decir que si intentamos acceder a example.com una infinidad de veces, tarde o temprano el request va a ir al 10.0.0.4, el cual nos responderá que no está disponible con un 500, 404, u otro error parecido, pero si volvemos a acceder el request va a ir a otro servidor y si nos va a responder, entonces cada determinada cantidad de requests tendremos alguno fallido.

Para tener HA y FaultTolerance tenemos otras formas, por ejemplo podríamos armar una estructura con 4 equipos, con 2 IP's flotantes, balanceando la carga entre esas dos IP's y el keepalived switcheando entre los servidores en caso de que tengan fallas. Este puede ser uno de los escenarios, pero no el único. Igual esto es material para otro post.

En el siguiente capítulo veremos como configurar un Web Application Firewall y configurar algunas reglas.

Espero volver a verte pronto! Saludos!
