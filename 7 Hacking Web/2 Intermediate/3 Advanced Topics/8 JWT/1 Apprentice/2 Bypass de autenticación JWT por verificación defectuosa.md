# Bypass de autenticación JWT por verificación defectuosa

Tags: #JWT #BurpSuite 

```bash 
En esta lab, se descubre un fallo en la verificación de JWTs donde el servidor acepta tokens sin firma si el campo 'alg' se define como 'none'. Se aprovecha esta configuración insegura para crear un token completamente manipulado: se modifica el campo 'sub' para suplantar al usuario administrador, se establece el algoritmo a 'none' y se elimina la firma dejando sólo el encabezado y el payload separados por un punto.

A pesar de carecer de firma, el servidor acepta el token y da acceso al panel de administración, desde donde se elimina al usuario Carlos. Este lab demuestra por qué permitir alg: none es una práctica peligrosa y cómo puede ser aprovechada por un atacante para evitar autenticaciones basadas en JWT.

   
    Header    .       Payload       .    Signature
     none     .    administrator    .
```