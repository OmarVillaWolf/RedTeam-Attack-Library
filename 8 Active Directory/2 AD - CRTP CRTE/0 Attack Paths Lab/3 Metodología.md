# Metodología CRTE 

Tags: #CRTE #Windows 

```bash 
- Enumeración del entorno Windows 
	- Enumeración de las variables del entorno, Dominio, Usuarios, Admins del dominio, Fores, Trust, Computadoras, GPO, OU
	- Enumeración SMB share 
	- SoapHound, SharpADWS para utilizar Bloodhound 
	- User hunting

- Escalada de privilegios locales 
	- Leaked Hunting 
	- Agregar al user de bajos privilegios a Admin local

- Movimiento Lateral 
  	- Verificar más grupos una vez siendo admin 
		- Verificar si un grupo al que se pertenece tiene ulguna ACL en otro grupo
	- Desactivar defensas de Windows 
	- Dumpeo de credenciales (Safetikatz)
	- ACLs 
	- Identificar sesiones remotas 
	- Certificados (Certify)
```