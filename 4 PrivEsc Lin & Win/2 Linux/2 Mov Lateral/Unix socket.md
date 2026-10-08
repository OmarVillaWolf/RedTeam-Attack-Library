# Unix Socket 

Tags: #Linux #MovimientoLateral 

```bash 
Paso 1:
# Buscar todos los unix socket del sistema 
❯ find / -type s -ls 2>/dev/null     #  La -type s busca específicamente archivos de tipo socket

# Buscar en rutas típicas donde se ponen sockets
❯ find /opt /var /run /tmp /srv -type s 2>/dev/null

Ejemplo:
	   262405      0 srwxrwx---   1 ronnie.stone bank-team        0 Oct  7 08:01 /opt/bank/sockets/live.sock


Resultados:
	/var/run/*.sock	    <-   Servicios del sistema (docker, mysql)
	/tmp/*.sock	        <-   Aplicaciones temporales
	/opt/<app>/	        <-   Aplicaciones custom — las más interesantes   <- IMPORTANTE 
	/run/*.sock	        <-   Servicios modernos (systemd)
```

```bash 
Paso 2: 
# Ver sockets activos con proceso escuchando
❯ ss -lx   # Muestra todos los Unix sockets en estado LISTEN con su ruta

# Ver todos los sockets incluyendo los conectados
❯ ss -ax | grep -i sock

Ejemplo: 
	u_str LISTEN 0 5 /opt/bank/sockets/live.sock 47026  * 0  
```

```bash 
Paso 3:
# Tener permisos de lectura/escritura sobre el socket
❯ ls -la /opt/bank/sockets/

Resultado: 
	srwxrwx--- 1 ronnie.stone bank-team    0 Oct  7 08:01 live.sock
```

```bash 
Paso 4: 
# Si se cumple todo lo anterior, podemos ejecutar el siguiente comando para cambiar de sesión 
❯ socat stdio unix-connect:/opt/bank/sockets/live.sock     # Colocar la ruta absoluta del socket 
```