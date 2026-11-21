# Cobalt Strike 

Tags: #CobaltStrike 

Cobalt Strike es un marco de comando y control líder en el mercado que proporciona un agente de posexplotación para simular actores integrados sigilosos y de largo plazo.  Se pueden modificar múltiples aspectos de las herramientas para disfrazarlas de malware conocido o servicios legítimos.  Tiene capacidades integradas para realizar múltiples TTP, incluido compromiso inicial, escalada de privilegios y movimiento lateral.  También tiene informes integrados y mapeo MITRE.

## Componentes 

* ``Beacon``: Es el agente de posexplotación de Cobalt Strike.  Una vez que se ejecuta en un punto final comprometido, es responsable de comunicarse con un servidor de equipo, obtener trabajos posteriores a la explotación, ejecutarlos y enviar los resultados.  Beacon en sí se implementa como una DLL de Windows, pero se puede empaquetar en cargas útiles de varios formatos, incluidos ejecutables, scripts de PowerShell y shellcode independiente de la posición.
* ``Team Server``: El servidor del equipo es un sistema central de control y registro.  Almacena información recopilada durante una interacción y crea varios modelos de datos que pueden usarse para generar informes y en algunos flujos de trabajo de Beacon.
* ``Client``: Los operadores del equipo rojo utilizan el cliente para conectarse a uno o más servidores del equipo para gestionar la interacción con las cargas útiles de Beacon.  Cuando se conecta a varios servidores del equipo al mismo tiempo, el cliente recopila y agrega los datos de todos ellos para proporcionar una única vista coherente de la interacción.

## Operaciones distribuidas

El patrón de diseño de Cobalt Strike para operaciones distribuidas consiste en instalar servidores de equipo dedicados para cada fase de un compromiso, como el acceso inicial, la posexplotación y la persistencia.  La razón es que si se descubre y bloquea parte de una operación, se pueden utilizar otros canales para mantener el acceso.  Cobalt Strike tiene múltiples características de consolidación de infraestructura que hacen que sea fácil trabajar con él.

