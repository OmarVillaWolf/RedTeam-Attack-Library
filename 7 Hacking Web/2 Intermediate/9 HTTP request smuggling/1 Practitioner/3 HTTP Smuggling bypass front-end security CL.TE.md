# Bypass de seguridad front-end (CL.TE)

Tags: #HTTP_Smuggling #BurpSuite 

```bash 
En este lab se aplica la técnica de HTTP request smuggling con una combinación CL.TE para evadir controles de acceso impuestos por el front-end. El objetivo es acceder al panel de administración, normalmente restringido, y eliminar al usuario carlos desde el back-end.

Para ello, se construye una petición maliciosa que el front-end interpreta como finalizada tras leer los bytes indicados por Content-Length, pero que en realidad contiene una segunda petición HTTP embebida. Esta segunda petición es procesada por el back-end de forma independiente y sin las restricciones del front-end, lo que nos permite interactuar con rutas protegidas como /admin o /admin/delete.
```

```bash  

```

```bash 


```