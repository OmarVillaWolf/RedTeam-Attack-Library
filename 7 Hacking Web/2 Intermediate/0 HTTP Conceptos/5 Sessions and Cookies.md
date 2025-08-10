# Cookies y sesión

Tags: #HTTP #Web #PortSwigger 

## ¿Qué es una cookie?

```bash 
- Pequeño fragmento de datos enviado por el servidor y almacenado en el navegador.  
- Se utiliza para mantener información entre peticiones HTTP (que por sí son sin estado).
- Se reenvía automáticamente al servidor en cada solicitud al mismo dominio.

# Ejemplo de encabezado de solicitud:
❯ Cookie: session=abcd123

# Ejemplo de encabezado de respuesta:
❯ Set-Cookie: session=abcd123; Path=/; HttpOnly
```

## Cookie de sesión

```bash 
- Asocia al usuario con una sesión activa en el servidor.  
- Generalmente tiene un ID único generado al iniciar sesión.
- Expira cuando se cierra el navegador (a menos que tenga fecha de expiración).
```

##  Atributos de seguridad de cookies

| **Atributo** | **Descripción**                                                                  |
| ------------ | -------------------------------------------------------------------------------- |
| HttpOnly     | Impide el acceso a la cookie desde JavaScript. Protege contra XSS.               |
| Secure       | La cookie solo se envía por conexiones HTTPS.                                    |
| SameSite     | Controla si la cookie se envía con solicitudes desde otros sitios (mitiga CSRF). |

## Metodos de SameSite 

| **Valor** | **Comportamiento**                                                               |
| --------- | -------------------------------------------------------------------------------- |
| Strict    | La cookie solo se envía si el sitio de origen coincide exactamente.              |
| Lax       | Se envía en la mayoría de los casos, pero no en peticiones POST de otros sitios. |
| None      | La cookie siempre se envía, incluso en peticiones cross-site. Requiere `Secure`. |