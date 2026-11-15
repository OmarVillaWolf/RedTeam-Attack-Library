## Service Creation 

Tags: #ServiceCreation #Persistencia 

```bash 
- High privs, start on boot
- sc utility 

# Ejecutar como administrador 
# Esto se ejecutará cada vez que se reinicie el server 
❯ sc.exe create Test_service binPath= "C:\Users\user1\persistence.exe" start= auto  
	# binPath = Ruta del ejecutable (Implante de Sliver C2)
```