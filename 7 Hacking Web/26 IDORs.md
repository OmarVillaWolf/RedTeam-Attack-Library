# Insecure Direct Object Reference (IDORs)

Tags: #IDORs #OWASP #Explotacion 

Las **Insecure Direct Object References** (**IDOR**) son un tipo de vulnerabilidad de seguridad que se produce cuando una aplicación web utiliza **identificadores internos** (como números o nombres) para identificar y acceder a recursos (como archivos o datos) y no se valida adecuadamente la autorización del usuario para acceder a ellos.

Por ejemplo, si una aplicación web utiliza un identificador numérico para identificar un registro en una base de datos, un atacante puede intentar adivinar este identificador y acceder a los registros sin la debida autorización. Esto puede permitir a los atacantes acceder a información confidencial, modificar datos, crear cuentas de usuario no autorizadas y realizar otras acciones maliciosas.

La vulnerabilidad IDOR se puede producir cuando una aplicación web **no implementa controles de acceso adecuados** para los recursos que maneja. Por ejemplo, una aplicación puede validar el acceso a través de autenticación y autorización para los recursos que se muestran en la interfaz de usuario, pero no aplicar la misma validación para los recursos que se acceden directamente a través de una URL.

Para explotar una vulnerabilidad IDOR, un atacante puede intentar modificar manualmente el identificador de un objeto en la URL o utilizar una herramienta automatizada para probar diferentes valores. Si el atacante encuentra un identificador que le permite acceder a un recurso que no debería estar disponible, entonces la vulnerabilidad IDOR se ha explotado con éxito. Esto también se le conoce como 'Parameter Tampering'.

Por ejemplo, supongamos que un usuario ‘**A**‘ tiene un pedido con el identificador numérico **123** y el usuario ‘**B**‘ tiene un pedido con el identificador numérico **124**. Si el atacante intenta acceder a través de la URL “**https://example.com/orders/124**“, la aplicación web podría permitir el acceso a ese pedido sin validar si el usuario tiene permiso para acceder a él. De esta manera, el atacante podría acceder al pedido del usuario ‘**B**‘ sin la debida autorización.

Para prevenir la vulnerabilidad IDOR, es importante validar adecuadamente la autorización del usuario para acceder a los recursos, tanto en la interfaz de usuario como en las solicitudes directas a través de URL. Además, se recomienda restringir los permisos de acceso a los recursos y mantener actualizado el software y los sistemas operativos.

A continuación, se comparten los enlaces correspondientes a los laboratorios que nos estaremos desplegando en esta clase para practicar esta vulnerabilidad:

-   **XVWA 1**: [https://www.vulnhub.com/entry/xtreme-vulnerable-web-application-xvwa-1,209/](https://www.vulnhub.com/entry/xtreme-vulnerable-web-application-xvwa-1,209/)
-   **SKF-LABS IDOR**: [https://github.com/blabla1337/skf-labs/tree/master/nodeJs/IDOR](https://github.com/blabla1337/skf-labs/tree/master/nodeJs/IDOR)


## IDORs

El IDOR se encuentra en la URL, en esta imagen miramos que solo podemos seleccionar hasta 5 cafes, pero si metemos otro numero en la URL como 6,7,8,9,10 podríamos ver mas cafes. Este tipo de vulnerabilidades son muy comunes en paginas del Gobierno.

![](Pasted%20image%2020230520144708.png)


En este otro lab, podemos observar que el segundo campo es el que vamos a interceptar con BurpSuite

![](Pasted%20image%2020230520145641.png)

Aplicaremos Fuzzing para descubrir mas identificadores y ver si encontramos mas documentos.
```bash
❯ wfuzz -c -X POST --hh=4520,1233 -t 200 -z range,1-1500 -d 'pdf_id=FUZZ' http://localhost:5000/download

	# z = Crear un payload de tipo rango
	# hh = Hidecharacters
	# d = data por POST
	# X = Indicamos que el metodo es POST

❯ wfuzz -c -X POST --hl=101,104 -z range,1-1500 -d 'pdf_id=FUZZ' http://localhost:5000/download
```