# Vulnerabilidades de control de acceso 

Tags: #AccessControl 

## Funcionalidad admin sin protección

```bash 
En este lab, el archivo 'robots.txt', utilizado para indicar a los motores de búsqueda qué rutas no indexar, revela accidentalmente la ubicación del panel de administración. Al acceder a la URL filtrada, se encuentra una funcionalidad crítica sin ningún tipo de autenticación.
```

```bash 
❯ nmap --script http-enum -p443 IP     # Enumeración 
```

## Funcionalidad admin con URL impredecible

```bash 
En este lab aunque el panel de administración no se encuentra en una ruta estándar, su ubicación se filtra en el código JavaScript de la página principal.
```

```bash 
❯ ctrl + u      # Mirar el código fuente de la web  
```