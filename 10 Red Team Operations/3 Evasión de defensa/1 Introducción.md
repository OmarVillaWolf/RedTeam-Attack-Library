# Defence Evasion 

Tags: #Evasion 

_La evasión de defensa es_ una táctica [TA0005](https://attack.mitre.org/tactics/TA0005/) en la que los adversarios intentan evitar ser detectados durante su operación.  Esto no es algo que hagan sólo al principio o al final de un compromiso, sino que se incorpora en todo momento.

La mayoría de los productos antivirus tienen dos estrategias principales para detectar actividad maliciosa.  El primero es utilizar firmas que apuntan a partes conocidas o estáticas de una carga útil.  Dentro del ecosistema Cobalt Strike, eso incluye la DLL sin procesar de Beacon, el cargador antepuesto, los artefactos de carga útil o todos ellos.  Esta estrategia se utiliza para detectar cargas útiles a medida que se descargan en el disco o se cargan en la memoria, ya sea antes o justo después de la ejecución.  El segundo es utilizar heurísticas del comportamiento.  Estos se utilizan para detectar acciones posteriores a la explotación una vez que una carga útil está en funcionamiento y pueden incluir cosas como generar nuevos procesos, ejecutar comandos de shell, inyección remota de procesos y ejecutar herramientas posteriores a la explotación de terceros.  Como operadores, necesitamos saber cómo modificar nuestras cargas útiles y comportamientos posteriores a la explotación para evitar estas detecciones. 

Los objetivos de este capítulo son aprovechar:

- El kit de artefactos para eliminar indicadores conocidos de los artefactos compilados (_.exe_ y _.dll_).
- El kit de recursos para eliminar indicadores conocidos de los artefactos del script (_.ps1_).
- C2 maleable para modificar la forma en que el cargador antepuesto carga Beacon en la memoria.
- C2 maleable para modificar comportamientos post-explotación.

