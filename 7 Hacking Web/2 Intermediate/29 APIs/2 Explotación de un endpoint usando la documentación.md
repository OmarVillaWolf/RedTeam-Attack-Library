# APIs

Tags: #APIs #API_Rest #BurpSuite 

```bash 
Una API es un conjunto de reglas y protocolos que permite que dos aplicaciones de software se comuniquen entre sí para intercambiar datos y funcionalidades.

Una API REST (Representational State Transfer Application Programming Interface) es una interfaz de programación que permite la comunicación entre sistemas, como un cliente y un servidor, a través de la web, utilizando el protocolo HTTP y los verbos estándares (GET, POST, PUT, DELETE) para manipular recursos.
```

## Explotación de un endpoint usando la documentación 

```bash 
En esta lab, se explora cómo una documentación de API mal protegida puede volverse un vector de ataque. Se accede al endpoint '/api', el cual revela una interfaz interactiva con operaciones disponibles. Desde esta documentación, se descubre la posibilidad de eliminar usuarios, y aprovechar esta funcionalidad para borrar un usuario.
```

```bash 
1. Capturar la solicitud al momento de cambiar el correo al usuario y muestra el método 'PATCH'
   PATCH /api/user/omar
   
2. Mirar los métodos permitidos en la api si esta mal configurada por medio de la url en la web usando el mètodo 'GET'
   - https://web.com/api/       

1. Elimiar el usuario utilizando el mètodo 'DELETE' en la petición de 'BurpSuite', quitando el contenido del body de la petición 
   DELETE /api/user/juan
```
