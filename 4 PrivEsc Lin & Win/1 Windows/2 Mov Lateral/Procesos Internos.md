# Procesos Internos 

Tags: #Windows #MovimientoLateral 

## Enumeración 
```powershell 
# Enumerar procesos 
❯ netstat -ano | findstr LISTENING

# Ejemplo:
	 TCP 127.0.0.1:1433 0.0.0.0:0 LISTENING 4188   # MSSQL LOCAL (Hacer Pivoting para traer el servicio a Kali)
	 TCP 127.0.0.1:80   0.0.0.0:0 LISTENING 4      # Servicio WEB (Hacer Pivoting para traer el servicio a Kali)
```


