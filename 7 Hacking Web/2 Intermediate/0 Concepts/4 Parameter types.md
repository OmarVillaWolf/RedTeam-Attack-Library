# Tipos de parámetros

Tags: #HTTP #Web #PortSwigger 

## Query parameters

```bash 
- Se envían en la URL después del símbolo ' ? '
- Sintaxis: ' ?clave=valor&otraClave=valor2 ' 
- Usados normalmente en solicitudes 'GET' 
- Se pueden modificar fácilmente desde el navegador

❯ https://ejemplo.com/productos?id=123&orden=asc
```

## Form parameters

```bash 
- Se envían en el 'cuerpo (body)' de la solicitud
- Típicamente en formularios con método 'POST' 
- Formato: 'application/x-www-form-urlencoded' o 'multipart/form-data'

❯ usuario=admin&password=123456
```

## JSON body

```bash 
- También enviado en el cuerpo (body) de la solicitud 
- Formato moderno usado en APIs REST
- Se usa junto con 'Content-Type: application/json'

{  
	"usuario": "admin",   
	"password": "123456" 
}
```

## Path parameters

```bash 
- Parámetros incrustados en la ruta de la URL
- Muy comunes en APIs RESTful
- No llevan '?' ni están en el body

❯ https://ejemplo.com/usuarios/123
```