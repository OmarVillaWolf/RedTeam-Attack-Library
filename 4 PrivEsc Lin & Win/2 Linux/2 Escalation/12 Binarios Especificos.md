# Abuso de binarios específicos

Tags: #Linux #BinariosEspecificos  #Escalada #Root #Privilegios #Pkexec 

Se analizará cómo elevar nuestros privilegios de usuario mediante la explotación de dos binarios diferentes como ejemplos ilustrativos.

El primer ejemplo se enfoca en explotar el binario legítimo **exim-4.84-7**, que presenta una vulnerabilidad identificada como **CVE-2016-1531**. Esta vulnerabilidad permite a un atacante ejecutar comandos privilegiados mediante el abuso de ciertas variables de entorno. Estudiaremos cómo aprovechar esta vulnerabilidad para escalar privilegios y acceder a funciones restringidas.

El segundo ejemplo aborda un **Buffer Overflow** en un binario personalizado en una máquina **Linux** de **32 bits** con **protecciones activas** y **ASLR** habilitado. En este caso, nos centraremos en explotar un **ret2libc** en un binario que posee permisos SUID y cuyo propietario es root. A través del buffer overflow, demostraremos cómo inyectar comandos privilegiados y, en consecuencia, elevar los privilegios de usuario.

La idea de esta clase es que sirva para demostrar cómo ciertos binarios, tanto legítimos como personalizados, pueden ser explotados para obtener privilegios elevados, lo que destaca la importancia de una adecuada configuración y protección en los sistemas.

A continuación, se os comparte el enlace de descarga de la máquina **Pluck** de Vulnhub, la cual estaremos utilizando para representar el primer caso de explotación:

- **Máquina Pluck de Vulnhub**: [https://www.vulnhub.com/entry/pluck-1,178/](https://www.vulnhub.com/entry/pluck-1,178/)

Por otro lado, os compartimos el enlace directo de descarga para Ubuntu 16.04.7 LTS (Xenial Xerus):

- **Ubuntu 16.04.7**: [https://releases.ubuntu.com/16.04/](https://releases.ubuntu.com/16.04/)

Por último, se os comparte el enlace de descarga al binario el cual estaremos explotando para este segundo caso. Este binario es necesario que lo depositéis en alguna ruta del sistema y que le otorguéis de permisos de ejecución:

- **Binario** **CUSTOM**: [https://hack4u.io/wp-content/uploads/2023/04/custom](https://hack4u.io/wp-content/uploads/2023/04/custom) (QUITADLE LA EXTENSIÓN TXT UNA VEZ DESCARGADO)

## Binarios Específicos 

```bash 
❯ find / -perm -4000 2>/dev/null                # Buscar archivos con permisos SUID

	# /usr/exim/bin/exim-4.84-7 = Es un agente de transporte de correo, y puede ser utilizado en la malloria de los sistemas UNIX
```