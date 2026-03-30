# Evasión de antivirus 

Tags: #AD #Evasion #Windows #HoaxShell 

```bash 
1. Ofuscación de Código y Cargas Útiles:
    - Polimorfismo y Metamorfismo: Estas técnicas involucran la generación de múltiples variantes de código cada vez que se ejecuta el malware, cambiando su apariencia sin alterar su funcionalidad. Esto ayuda a evitar la detección basada en firmas. 
    - Empaquetamiento y Encriptación: Empaquetar o encriptar una carga útil puede ayudar a ocultarla de los antivirus que buscan firmas de malware en archivos ejecutables en texto plano.
        
2. Living off the Land (LotL) y Fileless Attacks:
    - Estos ataques implican el uso de herramientas legítimas ya presentes en el sistema (como PowerShell, WMIC, o certutil) para ejecutar comandos maliciosos. Como no se introducen archivos ejecutables (fileless), la detección se vuelve mucho más difícil.
    - Los ataques sin archivos también pueden residir completamente en la memoria (RAM), evitando el disco por completo, lo que los hace menos visibles para los antivirus que monitorizan cambios en el sistema de archivos.
        
3. Evasión de Heurística y Detección Basada en Comportamiento:
    - Algunos antivirus usan heurística para detectar comportamientos anómalos o sospechosos, incluso si el código malicioso no coincide con una firma conocida. La evasión puede implicar dividir operaciones maliciosas en pasos más pequeños, espaciados en el tiempo, o disfrazarlos para que parezcan actividades legítimas.   
    - La manipulación de procesos y servicios legítimos, también conocida como "Process Hollowing" o "Process Doppelganging", puede ser utilizada para ocultar código malicioso dentro de procesos legítimos.
        
4. Abuso de la Lista de Permitidos y Excepciones:
    - Algunos sistemas pueden tener configuraciones de antivirus que establecen excepciones para ciertos programas o directorios. Los atacantes pueden aprovechar estos para ubicar sus herramientas maliciosas o ejecutar procesos en áreas que el antivirus ignora.
        
5. Técnicas de Sandbox Evasion:
    - Algunas soluciones de seguridad usan entornos de sandbox para ejecutar y analizar código sospechoso en un entorno seguro. Los atacantes pueden programar su código para que se comporte de manera diferente cuando detecta que está en un entorno de sandbox (por ejemplo, retrasando la ejecución, verificando la interacción con el usuario, o buscando indicadores típicos de una máquina virtual).
        
6. Actualizaciones y Adaptación Constante:
    - Dado que las soluciones de seguridad se actualizan constantemente con nuevas firmas y métodos de detección, la evasión de AV también requiere una adaptación y evolución constantes. Los atacantes necesitan mantenerse al día con las últimas defensas y desarrollar nuevas técnicas de evasión en respuesta.
```

## Utilizando HoaxShell

* [Hoaxshell](https://github.com/t3l3machus/hoaxshell)

```bash 
HoaxShell es una herramienta diseñada para generar shells inversas en PowerShell, utilizando técnicas avanzadas de ofuscación y cifrado para evadir la detección de la mayoría de los antivirus más antiguos. Por ejemplo, las shells inversas creadas con HoaxShell no eran detectadas por las versiones recientes de antivirus incluidas en Windows 11.

Para manejar la conexión de la shell inversa, HoaxShell implementa un oyente HTTP(S). Esto implica que configura un servidor web en un puerto determinado (por defecto, el puerto 8080) y espera conexiones entrantes. Una vez que se ejecuta la shell inversa en el sistema objetivo, este establece una conexión de retorno con el oyente, transfiriendo datos a través del protocolo HTTP.
```