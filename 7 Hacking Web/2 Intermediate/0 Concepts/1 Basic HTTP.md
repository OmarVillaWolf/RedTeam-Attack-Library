# HTTP - Fundamentos básicos

Tags: #HTTP #Web #PortSwigger 

## ¿Qué es HTTP?
- HTTP (HyperText Transfer Protocol) es un protocolo de comunicación basado en el modelo cliente-servidor.
- El cliente (normalmente un navegador) hace peticiones al servidor, y este responde con datos (como páginas HTML, imágenes, JSON, etc).

## Versiones comunes de HTTP
- **HTTP/1.1**: Usa texto plano, no permite múltiples peticiones por conexión de forma eficiente.
- **HTTP/2**: Usa formato binario, permite multiplexación (varias peticiones/respuestas al mismo tiempo), compresión de headers.

## Métodos HTTP

| Método      | Descripción                                                                                                   | Ejemplo cURL por consola                                                                                   |
| ----------- | ------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| **GET**     | Solicita datos del servidor. No modifica nada.                                                                | `curl http://<IP>/index.html`                                                                              |
| **POST**    | Envía datos al servidor (ej. formularios)                                                                     | `curl -s -X POST http://<IP>/login.php -d "name=omar&password=passwd" -v`                                  |
| **PUT**     | Crea o reemplaza un recurso en la URL especificada. Reemplaza completamente el recurso                        | `curl -s -X PUT http://<IP>/cmd.txt -d @cmdasp.aspx`<br>`curl http://<IP>/uploads/ --upload-file file.txt` |
| **DELETE**  | Elimina el recurso especificado del servidor.                                                                 | `curl -s -X DELETE http://<IP>/uploads/file.txt`                                                           |
| **PATCH**   | Aplica modificaciones **parciales** a un recurso existente. Similar a PUT, pero más granular.                 | `curl -s -X PATCH http://<IP>/user/1 -d "email=nuevo@correo.com"`                                          |
| **HEAD**    | Igual que GET, pero solo recupera los encabezados, no el cuerpo. Ideal para verificar existencia de recursos. | `curl -I http://<IP>/archivo.pdf`                                                                          |
| **OPTIONS** | Recupera los métodos y encabezados permitidos para un recurso.                                                | `curl -v -s -X OPTIONS http://<IP>/post.php`                                                               |
| **MOVE**    | Mueve un recurso de una ubicación a otra. Útil para cambiar nombres/extensiones.                              | `curl -s -X MOVE http://<IP>/cmd.txt -H "Destination: http://<IP>/cmd.aspx"`                               |

