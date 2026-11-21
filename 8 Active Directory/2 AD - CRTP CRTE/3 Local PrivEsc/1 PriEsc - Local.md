# Local Privilege Escalation (Enumeración)

Tags: #AD #Windows #Powershell #PrivEsc 

* Utilizar las notas número 4 ``PrivEsc Win`` para la escala de privilegios en Windows. 

```bash 
Hay diferentes maneras de escalar privilegios localmente en Windows:

1. Parches faltantes 
2. Despliegue automatico y 'AutoLogon' password en texto claro 
3. AlwaysInstallElevated (Cualquier usuario puede correr MSI como SYSTEM)
4. Servicio mal configurados 
5. DLL Hijacking y mas 
6. Kerberos y NTLM relaying 

NOTA:
	- Utilizar herramientas de escalación de privilegios puede desencadenar detecciones 

