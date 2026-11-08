# Bypass de autenticación JWT sin verificar la firma

Tags: #JWT #BurpSuite 

```bash 
En esta lab, se analiza un fallo crítico en la implementación de tokens JWT: el servidor acepta cualquier token sin verificar su firma. Esto significa que podemos modificar el contenido del token sin necesidad de conocer la clave secreta.

1. Modificar el campo 'Payload' al usuario 'administrator', accediendo así al panel de administración sin autenticación real. Desde allí, se ejecuta la acción de eliminar al usuario. Esta técnica demuestra la importancia de validar siempre la firma de un JWT para garantizar la integridad y autenticidad del token.

    Header    .       Payload       .    Signature
    aaaaaa    .    administrator    .      cccccc
```