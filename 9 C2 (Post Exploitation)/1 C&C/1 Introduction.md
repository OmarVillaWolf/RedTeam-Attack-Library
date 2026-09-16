# Introducción 'Command and Control' 

Tags: #C2 

```bash 
❯ https://howto.thec2matrix.com/
```

Es la comunicación usada por un atacante remoto para coordinar y controlar actividades a través de un sistema comprometido. Abarcando los métodos, protocolos e infraestructura que permite el envió de comandos, recibir data y manejar las operaciones de una manera coordinada. 

* Coordinación centralizada 
Los frameworks de C2 proveen un punto centralizado donde el equipo de Red Team puede manejar múltiples sistemas comprometidos. Esta coordinación es esencial para controlar el flujo de operación, emisión de comandos y mantener la comunicación a través de la red distribuida. 

* Persistencia y acceso remoto 
C2 permite a los equipos de Red Team establecer y mantener la persistencia en un equipo comprometido. Activa el acceso remoto, permitiendo interactuar con los sistemas objetivo, ejecutar comandos y llevar acabo actividades post-explotación. Esto incluye métodos para asegurarnos que el agente de C2 persista a través del reinicio del sistema u otras interrupciones. Técnicas comunes de persistencia  seria las configuraciones del 'startup' del sistema, crear tareas agenda-das o instalar backdoors. 

* Movimiento lateral 
Los frameworks de C2 facilitan el movimiento lateral a través de la red. Los equipos de Red Team usan C2 para comprometer sistemas adicionales, navegar a través de los segmentos de red y escalar privilegio como ellos podrían hacerlo en el mundo real. 

* Ataques Complejos de Escenarios 
Los frameworks de C2 son usados por los Red Team para ejecutar ataques complejos con múltiples 'stages'.  Esto incluye tareas como exfiltracion de datos, escalacion de privilegios y técnicas de evasión. 

* Componentes C2
	* C2 Server: Es un punto central donde los atacantes emiten comandos y envían procesos de datos al sistema comprometido. Puede ser hosteado en varias plataformas, como sistemas 'on-premise', servidores cloud  o archivos escondidos. 
	* C2 Agent: Es un software que se esta ejecutando en el sistema comprometido que conecta al C2 Server. Facilita el acceso remoto y la transmisión de data. 
	* Canal de comunicación: El protocolo y método a través del cual se comunica el C2 server con los agentes. Incluyen protocolos como HTTP(S), DNS, WebSockets, protocolos encryptados, ofuscación para evitar la detección.   

* Remote Control and Command Execution 
La función primaria de un C2 es activar el control remoto del sistema comprometido. Permitiendo a los operadores ejecutar comandos en los endpoints remotos, archivos de acceso, ejecutar scripts y ejecutar una variedad de acciones. 

* Payload Development 
* Logging and Reporting 
* Evasion Techniques 
* Automating and Scripting 
