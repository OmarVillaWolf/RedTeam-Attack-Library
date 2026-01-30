# Credenciales en descripción 

Tags: #AD #DefaultPasswords #Powershell #PowerView 

```bash 
La vulnerabilidad relacionada con las "Credenciales en descripción" en Active Directory (AD) no es una vulnerabilidad en el sentido tradicional de un fallo de software o un error de diseño, sino más bien una mala práctica de administración que puede ser explotada por actores maliciosos.

- 'Contexto': En muchos entornos de AD, es común que los administradores o técnicos de sistemas añadan comentarios o descripciones a las cuentas de usuario para ayudar a identificar o administrar esas cuentas. Estas descripciones a veces contienen información sensible, como contraseñas temporales, información de contacto o notas sobre el propósito o la configuración de una cuenta.

- 'El problema': El atributo "description" (descripción) en un objeto de AD es legible por defecto por todos los usuarios del dominio. Esto significa que cualquier usuario, incluso sin privilegios especiales, puede consultar este atributo para todos los objetos de AD. Si los administradores han guardado contraseñas o información sensible en este campo, esta información está esencialmente expuesta a cualquiera en el dominio.

- 'Explotación': Los atacantes o empleados maliciosos que tengan conocimiento de esta mala práctica pueden realizar consultas en AD para recuperar estos atributos de descripción y buscar información sensible. Herramientas como `PowerShell` o `ldapsearch` pueden usarse para extraer estos datos en masa.
```

## Powerview 

```powershell  
❯ Get-NetUser -properties name, description       # Mirar la descripción de los usuarios 

Nota:
	1. Se puede encontrar la password en la descripción del usuario con Powershell 
	2. Se puede encontrar la password en la descripción del usuario con BloodHound 
```