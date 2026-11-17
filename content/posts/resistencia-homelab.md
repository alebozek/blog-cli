+++
date = '2026-09-22T19:45:53+02:00'
draft = false
title = 'R3s1st3nc14 H0m3l4b 01'
+++
## Resistencia Homelab 01
Buenas, bichejos del silicio, hoy os traigo una pequeña guía de resistencia para **securizar** vuestro homelab doméstico o servidor cara al público.

#### Primero lo importante, los puertos
Como sabemos, los servicios más comunes suelen exponer un puerto a la red con el que podemos interactuar con él de distintas formas. El no controlar qué o quién puede acceder a ese puerto es un problema de seguridad.
Lo primero es hacer inventario. En sistemas Unix-like podemos usar el comando **ss**:
```sh {linenos=inline hl_lines=[:] style=emacs}
ss -tulnp
```
Con los argumentos especificamos que muestre tanto TCP como UDP, los puertos en escucha, que muestre los nombres de servicios de forma numérica y el proceso que ocupa ese socket en escucha.
Revisamos los puertos y hacemos una lista de lo que queremos exponer, lo que no, y lo que sólo sería mejor permitir en la LAN.

#### Firewall a prueba de balas

Nos vamos a nuestro firewall de confianza (en mi caso ufw) y gestionaremos las reglas de la siguiente forma:
```sh {title="ayuda.sh" linenos=inline hl_lines=[:] style=emacs}
# para ver las reglas con su número
ufw status numbered
# para permitir un puerto sin restricciones
ufw allow <puerto>/<tcp/udp>
# para denegar un puerto sin restricciones
ufw deny <puerto>/<tcp/udp>
# para permitirlo sólo desde la LAN u alguna red concreta
sudo ufw allow from <rango-red> to any port <puerto> proto <tcp/udp>
# y lo contrario para denegarlo
sudo ufw deny from <rango-red> to any port <puerto> proto <tcp/udp>
```
{{< gh-blockquote type="important" title="Docker">}}
Algunos usuarios de Docker os daréis cuenta de que algunos de los puertos que vuestros contenedores usan no aparecen en ufw al consultarlos
{{< /gh-blockquote >}}

#### Docker y consideraciones a tener en cuenta
Si usas Docker en tu homelab o servidor expuesto al público, debes tener en cuenta que Docker puede publicar puertos directamente y cada vez que montes un servicio te recomiendo fijar los puertos a la dirección en LAN en caso de que quieras poder acceder desde tu red o desde localhost si es un servicio que no hace falta que sea expuesto a otras redes.

Se recomienda también que si tenéis algún servicio como por ejemplo SSH, hay que tener un poquitín de cabeza y no exponerlo a internet a menos que tengas contramedidas como el acceso por clave privada. Durante unos meses esto se me pasó y mi servidor estuvo recibiendo unos 3000 intentos de login diarios de bots y escáneres de red en el puerto 22.

Tampoco sería mala idea usar algún proxy inverso como Caddy, Traefik o Nginx para centralizar los accesos a aplicaciones web que estén hosteadas en el servidor en caso de tener más de una.

Recuerda que cuantos menos puertos y servicios tengamos habilitados en el servidor, menor es la superficie de ataque en un posible ataque

Un saludo y Happy Hacking!