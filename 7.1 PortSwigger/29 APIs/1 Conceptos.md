# Conceptos 

Tags: #APIs 

* [Postman](https://www.postman.com/downloads/)

## APIS

```bash 
Una 'API (Application Programming Interface)' es un conjunto de reglas y rutas (endpoints) que permiten que una aplicación se comunique con otra. Ademas, una API ofrece accesos organizados a diferentes acciones que una app puede hacer, como obtener datos, crear, modificar o eliminar recursos
```

| Web APIs           | Web Services         |
| ------------------ | -------------------- |
| SOAP               | REST                 |
| xml                | json, xml, yaml, txt |
| Operación = método | Operación = EndPoint |
| SOAPUI             | POSTMAN              |

```bash 
En 'Web Services' hay parámetros que son datos que le pasas a ese endpoint para afinar la respuesta o acción. Un 'endpoint' es una URL que representa un recurso o acción.

- https://web.com/apiv2/login 
```

## Rutas por defecto en la web

```bash 
- https://web.com/docs       # Documentación común en frameworks como FastAPi,Nesjs...
- https://web.com/redoc      # Documentación

- https://web.com/api/docs 
- https://web.com/v1/docs 


- FastAPI:
    - http://localhost:8000/docs (Swagger UI)  
    - http://localhost:8000/redoc (ReDoc)
        
- NestJS:
    - http://localhost:3000/api o /docs
        
- Spring Boot con Swagger:
    - http://localhost:8080/swagger-ui.html
```

## Métodos usados en las APIs 

|Método|Descripción|Ejemplo|
|---|---|---|
|`GET`|Obtener datos. No modifica nada.|Obtener lista de usuarios o un solo usuario.|
|`POST`|Crear un nuevo recurso.|Crear un nuevo usuario.|
|`PUT`|Actualizar un recurso completo.|Reemplazar toda la info de un usuario.|
|`PATCH`|Actualizar parcialmente un recurso.|Cambiar solo el email de un usuario.|
|`DELETE`|Eliminar un recurso.|Borrar un usuario de la base de datos.|