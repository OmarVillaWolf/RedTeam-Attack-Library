# Bypass de autenticación JWT por inyección en jwk

Tags: #JWT #BurpSuite #Asimétrico #JWK

```bash 
1. Descargar el 'JWT Editor' en Burpsuite
```

```bash 
En esta lab, se aborda una vulnerabilidad en la verificación de JWTs relacionada con el uso del parámetro 'jwk' en el encabezado del token. El servidor permite que el propio JWT contenga la clave pública con la que será validado, pero no comprueba si dicha clave proviene de una fuente confiable.

Se aprovecha esta debilidad generando un par de claves RSA y firmando un token falso que suplantará al usuario administrador. Al incluir nuestra clave pública dentro del 'jwk' del encabezado, el servidor valida la firma sin cuestionar su origen, otorgándonos acceso al panel de administración.

Esta técnica ilustra los peligros de confiar ciegamente en datos controlados por el cliente en mecanismos de autenticación.
```

## Crear un JWK con llave pública Asimétrico válido con 'JWT Editor' en Burpsuite 

```bash 
1. En algoritmos asimétricos se puede usar 'JWT Editor':
	- Dar click en 'New RSA Key'
	- Seleccionar 'JWK' y dar click en 'Generate' y dar click en 'ok'
```

## Crear un JWT válido embebido

```bash 
1. En la parte de 'JSON Web Token' del Proxy se modifica el 'Payload' agregando el usuario administrator
   
    Header    .       Payload       .    Signature
    aaaaaa    .    administrator    .      cccccc

2. Para embeber el 'JWK' en la cabecera 'Header' del JWT se da click en 'Attack > Embedded JWK' y se selecciona el antes creado 
```
