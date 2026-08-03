# Parameter pollution en query string del servidor

Tags: #APIs #API_Rest #BurpSuite 

```bash 
En esta lab, se comienza explorando cómo las aplicaciones que construyen internamente peticiones a APIs pueden ser vulnerables a Server-Side Parameter Pollution. Se analiza cómo añadir parámetros adicionales codificados con '%26*' (carácter '&') puede ser interpretado como una inyección de parámetros por el servidor.

Este comportamiento se identifica gracias a los mensajes de error que van revelando qué parámetros internos existen, permitiéndonos deducir la estructura del backend.
```

```bash 
1. Hay una petición llamada 'Forgot-password' por el método 'POST' que envia dos parámetros los cuales son csrf y username mirandola desde el 'BurpSuite'
   csrf=12345&username=omar
   
2. Se puede modificar el cuerpo de la petición agregando otra variable con el fin de ver los errores en la respuesta y así identificar posibles APIs
   csrf=12345&username=omar&test=test 
   csrf=12345&username=omar%26test=test 
   csrf=12345&username=omar%23            --->  Colocar un # 
   csrf=12345&username=omar&26field=test%23
```
