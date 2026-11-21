# Listeners 

Tags: #CobaltStrike 

El primer paso para configurar Cobalt Strike es crear uno o más listeners.  Un listener define el protocolo y los parámetros mediante los cuales una carga útil de Beacon se comunicará con el servidor del equipo.  Los protocolos proporcionados por Cobalt Strike de forma predeterminada son DNS, HTTP, HTTPS, SMB y TCP.  Estos proporcionan un medio para disfrazar la actividad de red generada por un Beacon para que se parezca al tráfico que uno normalmente esperaría ver en una red corporativa.
# Tipos de Listeners 

Estos listeners también se pueden dividir en dos tipos.

* ``Egress``: Como regla general, una baliza de salida se comunicará directamente con un servidor de equipo a través del límite de red del entorno de destino.  DNS y HTTP/S son listeners de salida.
* ``Peer-to-Peer``: Una baliza P2P no se comunica directamente con el servidor del equipo, sino que su tráfico se enruta a través de otra baliza.  Se pueden encadenar varias balizas P2P, pero en última instancia se vincularán a una baliza de salida para que el tráfico llegue al servidor del equipo.  SMB y TCP son los listeners P2P.

## 1 HTTP Listener 

El listener HTTP le indica a Beacon que se comunique con el servidor del equipo a través de solicitudes HTTP GET y/o POST.  De forma predeterminada, utilizará GET para recuperar tareas del servidor del equipo y POST para enviar los resultados.  El servidor del equipo inicia un servidor web integrado para atender las solicitudes.

![[Pasted image 20261121160957.png]]
### HTTP Hosts 

Estos son los hosts a los que Beacon enviará sus solicitudes HTTP, que pueden proporcionarse como direcciones IP o nombres de dominio.  Puede utilizar IP o dominios que se resuelvan directamente en el servidor del equipo; o IP/dominios que se resuelvan en un redirector.  Un redirector es un host intermediario que se encuentra entre Beacon y un servidor de equipo, y envía tráfico entre ambos.  Este curso no cubrirá ampliamente los redireccionadores, aunque las opciones de software populares para actuar como redireccionadores incluyen iptables, socat, Apache y NGINX.

### Host rotation strategy 

Si se proporciona más de un host HTTP, la estrategia de rotación le indica a Beacon cómo debe usar cada uno.  Estos son útiles para contrarrestar las técnicas de análisis de frecuencia para encontrar tráfico C2 sistemático y brindar resiliencia en casos en los que uno o más hosts HTTP están bloqueados.  Las opciones son **round-robin**, **aleatorias**, **conmutación por error** y **rotación**.

* ``Round robin``: Beacon simplemente recorre la lista de arriba a abajo.  Cada host se utiliza para una solicitud antes de pasar a la siguiente.
* ``Random``: Beacon selecciona aleatoriamente un host diferente para cada solicitud.
* ``Failover``: Viene en varios sabores:  **failover-x** y **failover-m/h/d**.  Esto le indica a Beacon que use el mismo host hasta que se cumpla la condición de conmutación por error seleccionada: ya sea la cantidad de intentos fallidos consecutivos, x; o fallas continuas durante el período de tiempo dado en minutos, horas o días.  Luego, Beacon pasará al siguiente anfitrión hasta que no queden más.
* ``Rotate``: Esta estrategia viene en una sola versión: **rotar-m/h/d**.  Similar al round-robin, pero cada host solo se usa durante el período de tiempo determinado antes de pasar al siguiente host de la lista.

### Max retry strategy 

Esto configura la estrategia de autodestrucción de Beacon en los casos en que todos los hosts HTTP se pierden o se bloquean (es decir, perdemos por completo nuestra capacidad de comunicarnos con él).

* ``None``: Beacon no saldrá y continuará funcionando indefinidamente a menos que se cancele por otros medios.
* ``Exit``: La sintaxis de esta configuración parece confusa, pero va **exit-[max_attempts]-[increase_attempts]-[duration][m/h/d]**, donde:

	- **max_attempts** es el número de intentos fallidos consecutivos antes de que Beacon salga.
	- **increase_attempts** es el número de intentos fallidos consecutivos antes de que Beacon aumente su tiempo de sueño.
	- **la duración** es el número de horas, minutos o días para establecer el nuevo tiempo de sueño.

Por ejemplo, **exit-50-25-1h** le indica a Beacon que aumente su tiempo de sueño a 1 hora después de 25 intentos fallidos y luego que salga si llega a 50 intentos fallidos.

### HTTP host (stager)

Este host solo lo utilizan las cargas útiles del stager.  Incluso si tiene varios hosts HTTP configurados, una carga útil de stager solo puede usar un único host para obtener la etapa de carga útil completa.  Puede utilizar la misma IP/dominio que una entrada de host HTTP existente o utilizar un host completamente diferente, siempre que se resuelva en el servidor de su equipo.

### Profile 

Los perfiles C2 maleables se pueden configurar con múltiples variantes de tráfico, que se pueden seleccionar desde este menú desplegable.  No verás nada más que el valor predeterminado a menos que hayas cargado un perfil que tenga múltiples variantes configuradas en él.

### HTTP por (C2)

Este es el puerto HTTP en el que Beacon intentará conectarse al servidor del equipo.  A menos que desee utilizar un puerto no estándar, en la mayoría de los casos lo mantendrá en línea con un puerto apropiado para el protocolo.  Por ejemplo, 80 u 8080 para HTTP.

### HTTP port (bind)

Este es el puerto al que el servidor del equipo vinculará su servidor web integrado.  Si no se especifica ninguna opción, utilizará el mismo puerto que el anterior.  Solo querrás configurar esto en un puerto diferente para realizar la flexión del puerto, es decir, usar un redirector que escuchará en el puerto C2, pero redirigirá el tráfico al servidor de tu equipo en un puerto diferente.  Esto es útil para ejecutar múltiples listeners HTTP en el mismo servidor de equipo, pero manteniendo todas sus Beacons hablando en el puerto 80.

### HTTP host header 

Al configurar un encabezado de host aquí, se propagará a la carga útil de Beacon sin tener que configurarlo explícitamente dentro de un perfil C2 maleable.   Esto hace que sea más conveniente aprovechar la interfaz de dominio sin codificar el dominio en ninguno de sus perfiles.

### Proxy HTTP 

De forma predeterminada, Beacon intentará utilizar la configuración de proxy de Internet del sistema en el que se ejecuta.  Sin embargo, puede codificar la información del proxy HTTP o SOCKS para que Beacon la utilice en su lugar si es necesario.  Esta configuración no se aplica a los escenarios de carga útil.

### Guardrails 

Las barandillas evitan que las cargas útiles Beacon sin etapas funcionen a menos que se cumplan los criterios especificados.  Esto es útil en los casos en que sus cargas útiles se copian o reenvían fuera del entorno de destino.  Dos escenarios posibles incluyen un correo electrónico de phishing reenviado de una víctima a una parte externa; o un miembro del equipo azul que intenta analizar la carga útil en un entorno aislado.   Las opciones disponibles son: la dirección IP del host; el nombre de usuario de la cuenta; el nombre del host; y el nombre del dominio al que está unido el host.

## 2 DNS Listener

El listener de DNS le indica a Beacon que se comunique con un servidor de equipo a través de solicitudes de DNS, específicamente búsquedas de registros A, AAAA o TXT.  El servidor del equipo inicia un servidor DNS integrado para atender las solicitudes.

![[Pasted image 20261121161339.png]]

Para que DNS Beacon funcione, debe agregar los registros DNS necesarios para que el servidor de su equipo tenga autoridad para uno o más subdominios.  En la mayoría de los casos, una parte como su registrador de dominio tendrá autoridad para su TLD de dominio.

DNS Beacon ingresa al servidor del equipo realizando una búsqueda de registro A para una configuración de dominio en el listener DNS.  A diferencia de otras balizas, como la baliza HTTP, la baliza DNS no transmite sus metadatos completos de inmediato.  Esto hace que las nuevas balizas DNS aparezcan inicialmente como filas 'vacías' sin información.  Los metadatos no se envían hasta que se le asigna una tarea al Beacon. Hay un comando simple`checkin` que no hace nada más que transmitir los metadatos del Beacon.
### DNS resolver 

De forma predeterminada, una baliza DNS utilizará cualquier solucionador de DNS con el que esté configurado el equipo en el que se ejecuta.  Puede sobrescribir esto con un solucionador de DNS personalizado ingresando su IP o nombre de host en este campo.

## 3 SMB Listeners 

El listener SMB es el primer ejemplo de un listener que no se vincula ni escucha en la máquina virtual del servidor del equipo; solo sirve como plantilla para la generación de carga útil.

![[Pasted image 20261121161427.png]]

Cuando se ejecuta, una carga útil de SMB Beacon creará una tubería con nombre SMB utilizando el nombre de **tubería** especificado en la configuración del listener.  Luego se requiere otra baliza para conectarse a esa tubería nombrada y retransmitir el tráfico entre la baliza SMB y el servidor del equipo.  Esto se hace a través del protocolo SMB de Windows en el puerto 445.

Cobalt Strike utiliza de forma`msagent_##` predeterminada`##` valores hexadecimales aleatorios.   Sin embargo, puede utilizar cualquier nombre de tubería que desee, siempre y cuando no coincida con un nombre existente en el host.  También puede crear múltiples listeners SMB con diferentes nombres de tuberías.

## 4 TCP Listeners 

Al igual que el listener SMB, el listener TCP no ordena al servidor del equipo que escuche en ningún puerto TCP.  Proporciona información de configuración al generar cargas útiles de TCP Beacon.

![[Pasted image 20261121161504.png]]

Cuando se ejecuta, una carga útil de TCP Beacon se vinculará y escuchará en el puerto C2 especificado en la configuración del listener.  Luego requiere que otra baliza se conecte a ese puerto y retransmita el tráfico entre la baliza TCP y el servidor del equipo.  **Si no se marca la opción Vincular solo al host local, Beacon se vinculará a 0.0.0.0**.  Si está marcado, se vinculará a **127.0.0.1**.

Se podría utilizar un listener TCP que se vincule a 0.0.0.0 para el movimiento lateral; y se podría utilizar un listener que se vincule a 127.0.0.1 para la escalada de privilegios.  Puede crear múltiples listeners TCP con diferentes números de puerto y configuraciones de enlace.