# Escalación de privilegios en Windows

Tags: #Windows #PrivEsc #Meterpreter

## Identificación de vulnerabilidades 

* [PrivescCheck](https://github.com/itm4n/PrivescCheck)
```bash 
# Clonamos el repo y pasamos todo el contenido a la maquina victima, dentro de la carpeta en la maquina victima hacemos lo siguiente:

❯ powershell -ep bypass -c ". .\PrivescCheck.ps1; Invoke-PrivescCheck"    # Recopila informacion del PrivEsc
```