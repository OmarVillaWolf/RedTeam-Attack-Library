# Interactuando con Beacons

Tags: #CobaltStrike 

Cuando una nueva sesión de Beacon ingresa por primera vez al servidor del equipo, aparecerá un registro en el Registro de eventos (**Ver > Registro** de eventos) y aparecerá una nueva sesión en la vista de tabla o gráfico, según cuál esté seleccionada.

![[Pasted image 20261122190943.png]]

Las columnas en la vista de tabla se explican por sí solas, pero aquí hay un resumen:

- **external** es la dirección IP externa del destino.  Esto lo resuelve el servidor web de Cobalt Strike, no lo envía el propio Beacon.
- **internal** es la dirección IP interna de la computadora.
- **listener** es el nombre del oyente de salida a través del cual se comunica Beacon.
- **user** **es** el nombre de usuario con el que se ejecuta el proceso Beacon.
- **computer** es el nombre de host de la computadora.
- **note** es donde puedes agregar tus propias notas.
- **process** es el nombre del proceso en el que se ejecuta Beacon.
- **pid** es el ID del proceso en el que se ejecuta Beacon.
- **arch** es la arquitectura Beacon, ya sea x64 o x86.
- **last** es la última vez, en ms, s, m, h o d, que Beacon se comunicó con el servidor del equipo.
- **sleep** es el tiempo de sueño del Faro.

El icono del monitor azul indica que el Beacon está funcionando en integridad media (es decir, privilegios de usuario estándar).  Cuando se ejecuta en alta integridad (es decir privilegios de administrador local o SISTEMA), se muestra un ícono de monitor rojo y al nombre de usuario se le agrega un asterisco.

![[Pasted image 20261122191157.png]]

Para 'interactuar' con una baliza, haga doble clic en su fila o haga clic derecho y seleccione **Interactuar**.  Esto abrirá una nueva pestaña.

![[Pasted image 20261122191230.png]]

Para obtener una lista de comandos, escriba el`help` cuadro de texto en la parte inferior y presione Enter.  Esto mostrará un breve resumen de cada comando.

```bash 
beacon> help

Beacon Commands
===============

    Command                   Description
    -------                   -----------
    [...snip...]
    getuid                    Get User ID
```

Para obtener más ayuda sobre un comando específico, utilice`help <command>` .

```bash 
beacon> help getuid
Use: getuid

Prints the User ID associated with the current token
```

Para asignarle una tarea al Beacon, simplemente escriba un comando válido y presione Enter nuevamente.  Esto colocará un trabajo en la cola.

```bash 
[02/26 15:52:55] beacon> getuid
[02/26 15:52:55] [*] Tasked beacon to get userid
```

Se pueden poner en cola varios comandos mientras espera que Beacon se registre.  La cola se puede borrar con el`clear` comando.  Si hay trabajos disponibles cuando Beacon se registra, el servidor del equipo los proporcionará en la respuesta de tráfico.  El cliente también imprime un pequeño registro para indicar que se enviaron trabajos.

```bash 
[02/26 15:52:59] [+] host called home, sent: 8 bytes
```

Beacon procesará cada trabajo en el orden en que se pusieron en cola y enviará los resultados.  Luego, el cliente muestra la salida al operador.

```bash 
[02/26 15:52:59] [*] You are DESKTOP-FGSTPS7\Attacker
```

## Session Graph View

La siguiente imagen muestra una salida y dos balizas P2P, pero no es inmediatamente obvio qué baliza está conectada a cuál.  ¿Ambas balizas P2P están conectadas directamente a la baliza HTTP o están dispuestas de forma diferente?

![[Pasted image 20261122191423.png]]

Aquí es donde entra en juego la vista gráfica.  Aunque muestra menos información que la vista de tabla, la relación entre cada baliza se vuelve obvia.

![[Pasted image 20261122191447.png]]

El ícono del firewall y la línea discontinua representan balizas de salida que se comunican directamente con el servidor del equipo.  Las líneas continuas representan conexiones P2P.  Los colores también son representativos del protocolo utilizado. Ellos son:

- Verde discontinuo para HTTP/S.
- Amarillo sólido para PYMES.
- Amarillo discontinuo para DNS.
- Verde sólido para TCP.

Haga clic derecho en algún lugar del fondo negro para que aparezca un menú que contiene opciones de diseño alternativas.  El diseño predeterminado que se ve aquí es Ár**bol > Izquierda**.  También puede hacer clic derecho en una baliza para que aparezca el mismo menú de interacción que ve en la vista de tabla.