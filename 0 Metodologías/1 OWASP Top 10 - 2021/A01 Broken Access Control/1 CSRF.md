# CSRF  (Cross-Site Request Forgery)

Tags: #OWASP #CSRF 

## Descripción 

```bash
El 'CSRF (Cross-Site Request Forgery)' engaña a un usuario autenticado para que realice acciones no deseadas en una aplicación web en la que está logueado, sin que se dé cuenta. 
```

## Impacto 

```bash 
# Impacto principal
	- El atacante 'aprovecha la sesión activa' del usuario para ejecutar acciones como si fuera él dando de alta, baja o actualizar datos. Estas peticiones serán enviadas a los usuarios desde enlaces o pueden estar ocultas dentro de las inyecciones CSRF, las cuales al ser accedidas por los mismos usuarios se ejecutarán con sus privilegios en sus sesión activa. 
    
### Posibles consecuencias:
	- Cambio de contraseña o correo electrónico.
	- Realizar transferencias de dinero.
	- Publicar o enviar mensajes sin autorización.
	- Borrar o modificar datos importantes.
	- 'Escalada de privilegios', si la víctima es un administrador.

# Riesgo Técnico
	- Impacto: Alto 
	- Probabilidad: Media 
	- Riesgo: Alto 
```

## Mitigación

```bash 
1. Token anti CSRF:
	- Generar un token único y aleatorio por sesión 
	- Cookie de sesión más CSRF token ligados al usuario  
```