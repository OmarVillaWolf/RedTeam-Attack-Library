# Introduccion a Sysinternals

Tags: #AD #Windows #Evasion 

Los binarios de Sysinternals son un conjunto de herramientas diseñadas para facilitar el trabajo de los administradores de sistemas. Originalmente desarrolladas por Mark Russinovich y Bryce Cogswell, estas utilidades fueron posteriormente adquiridas por Microsoft. Proporcionan una variedad de funcionalidades destinadas a diagnosticar, resolver problemas y monitorear sistemas operativos Windows de manera eficiente.

* [Sysinternals](https://learn.microsoft.com/en-us/sysinternals/)

```bash 
1. Enumeración y Reconocimiento: Herramientas como 'PsInfo' pueden proporcionar detalles sobre un sistema, lo que permite a un atacante o a un hacker ético entender mejor el entorno que están explorando.
    
2. Manipulación de Procesos: Con herramientas como 'ProcDump', 'Process Explorer' y 'PsKill', los atacantes pueden volcar la memoria de un proceso, explorar procesos en ejecución con detalle o matar procesos, respectivamente.
    
3. Monitoreo de Actividad del Sistema: 'ProcMon' permite a los usuarios monitorear actividad de archivos, registro y procesos en tiempo real. Esto puede ayudar a entender cómo funciona un programa o detectar actividad sospechosa.
    
4. Gestión de Sesiones y Logon: 'PsLoggedOn' puede mostrar quién está logueado en un sistema y a través de qué medios, lo que puede ser útil para determinar otros posibles vectores de ataque o entender el comportamiento del usuario.
    
5. Movimiento Lateral: Herramientas como 'PsExec' permiten la ejecución de procesos en sistemas remotos, lo cual es invaluable para el movimiento lateral dentro de una red.
    
6. Evasión: Algunos de los binarios de Sysinternals pueden ser usados de formas creativas para evadir soluciones de seguridad o para ejecutar comandos de maneras que no son típicamente monitoreadas.
```