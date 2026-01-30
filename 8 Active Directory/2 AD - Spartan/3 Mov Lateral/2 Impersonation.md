# Impersonation 

Tags: #AD #Windows 

La impersonación en ciberseguridad, particularmente en entornos de Active Directory, se refiere al uso de credenciales obtenidas para acceder a recursos actuando como si fuera otro usuario. Dependiendo del tipo de credencial obtenida, la impersonación puede llevarse a cabo de diversas formas.

```bash 
1. Usando Hashes de Contraseñas LM o NT: Técnica conocida como 'pass-the-hash'.
2. RC4 Kerberos key (es decir, hash NT): Conocida como 'overpass-the-hash'.
3. no-RC4 Kerberos key (como DES o AES): Referida como 'pass-the-key', también un alias para 'overpass-the-hash'.
4. Ticket Kerberos: A través de la técnica 'pass-the-ticket'.
5. Contraseña en Texto Plano: Se aplican técnicas adicionales.
```

## Formas de hacerlo 

```powershell 
Es un comando estándar de Windows que permite ejecutar un programa bajo una cuenta de usuario diferente. Utiliza el flag '/netonly' para indicar que las credenciales se usan solo para acceso remoto.

❯ runas /netonly /user:$DOMAIN\$USER "powershell.exe"


- Objeto de Credencial en PowerShell: Puedes crear un objeto de credencial en PowerShell y suministrarlo con el argumento '-Credential' en el siguiente comando. Esto se puede hacer de manera interactiva con 'Get-Credential' o no interactiva creando el objeto de credencial directamente​​.

❯ Get-Credential      # Pasamos el nombre y passwd del usuario 


- Funciones de PowerView: Muchas funciones de PowerView admiten los parámetros '-Credential', '-Domain' y '-Server', que se pueden utilizar para especificar explícitamente el usuario, el dominio y el controlador de dominio objetivo. Aquí también, se debe proporcionar un objeto de credencial​​.
```