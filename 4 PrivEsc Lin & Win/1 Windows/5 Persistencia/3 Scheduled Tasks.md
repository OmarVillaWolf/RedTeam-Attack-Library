## Scheduled Task 

Tags: #ScheduledTasks #Persistencia 

```bash 
- schtasks
- Built-In Utility
- Run binary on startup 

# Ejecutar la tarea como administrador 
❯ schtasks /create /tn "Test" /tr "C:\Users\user1\persistence.exe" /sc onstart /ru SYSTEM
	# tn = Nombre de la tarea 
	# tr = Ruta del ejecutable (Implante de Sliver C2)
	# sc = Ejecutar cada vez que se reinicie el sistema 
```