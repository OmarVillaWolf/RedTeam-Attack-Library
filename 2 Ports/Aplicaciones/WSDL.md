# WSDL 

Tags: #WSDL

Cuando nos enfrentamos con los servicios de seguridad de la web, accesar al archivo WSDL  es el primer paso, esto nos dará una lista completa de las operaciones y tipos permitidos por el servidor así como la syntax correcta para usar, entradas, salidas y toda la información adecuada para poder ejecutar correctamente los ataques.  
Antes de enumerar el archivo WSDL para el servicio de web SOAP, necesitamos identificar el servicio web SOAP y los endpoints. 

```bash 
❯ http://soap.site/ws-user-account.php?wsdl       # En la url al colocar '?wsdl' podemos identificarlo y saber si existe 
```

Una vez identificado el archivo WSDL, podemos comenzar inspeccionándolo y recopilar información de valor a cerca del servicio web como operaciones, data, syntax y mas.   
