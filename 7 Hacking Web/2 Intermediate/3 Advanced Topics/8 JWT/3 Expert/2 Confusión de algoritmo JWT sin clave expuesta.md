# Confusión de algoritmo JWT sin clave expuesta

Tags: #JWT #BurpSuite #Sig2n

* [Sig2n](https://hub.docker.com/layers/portswigger/sig2n/latest/images/sha256-0f1a6583c2578ffc42b7f3ee3a7f718c2979bc5b83ba7e125197b368f67b26d9)

```bash 
En este lab, existe una confusión de algoritmos en JWT sin que el servidor exponga públicamente su clave. A pesar de ello, se logra deducir la clave pública RSA que utiliza para validar los tokens, a partir de dos JWT generados legítimamente tras iniciar sesión.

Se usa la herramienta 'sig2n', que permite reconstruir valores matemáticamente posibles de la clave pública analizando las firmas. Tras identificar la clave válida, la usamos como si fuera una clave secreta con el algoritmo HS256, firmando un token falso que nos identifica como administrador.

El servidor acepta el token y se accede al panel de administración. Este lab demuestra cómo incluso sin exposición directa de claves, la criptografía mal gestionada puede ser vulnerada mediante análisis estructurales del propio token.
```

## Sig2n

```bash 
❯ docker run --rm -it portswigger/sig2n 'token1' 'token2'   # Genera varios valores de clave pública y varios JWT validos


Notas: 
	1. Los tokens para el comando se obtienen de la misma sesión haciendo login dos veces 
	2. Una vez validado el JWT en el 'Repeater' se puede crear una clave simétrica usando el valor de la clave pública con su valor PEM en 'base64' 
```

## Crear un JWK Simétrico válido con 'JWT Editor' en Burpsuite 

```bash 
1. En algoritmos simétricos se puede usar 'JWT Editor':
	- Dar click en 'New Symetric Key'
	- Dar click en 'Generate'
	- Sustituir en la variable 'k' el resultado de la clave pública calculada por 'sig2n' en 'base64' y dar click en 'ok'


Notas:
	1. Es importante validar el JWT asignado a esa clave pública anteriormente 
```

## Crear un JWT válido 

```bash 
1. En la parte de 'JSON Web Token' del Proxy se modifica el 'Header' agregando lo siguiente debajo en 'alg':
   
   "alg": "HS256"

2. Modificar el campo del 'Payload':
   
    Header    .       Payload       .    Signature
    aaaaaa    .    administrator    .      cccccc
    
3. Para finalizar, se firma dando click en 'sign' seleccionando el JWK creado anteriormente y dando click en 'ok'
```