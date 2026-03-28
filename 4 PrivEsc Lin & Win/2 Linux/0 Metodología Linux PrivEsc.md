# Metodología — Linux Privilege Escalation

Tags: #PrivEsc #Linux #Metodologia #Escalada #Root

## OBJETIVO
Tener el mapa completo de enumeración y escalada de privilegios en Linux.
Saber exactamente qué buscar en cada fase y a qué técnica recurrir según lo encontrado.
Los comandos están en las notas individuales de cada técnica.

## TIPS 
1. **LinPEAS primero si puedes subirlo → pero entiende lo que hace → no dependas ciegamente de él**
2. **Enumera TODO antes de explotar → un vector más sencillo puede estar más adelante**
3. **Anota cada hallazgo → versiones, rutas, permisos, usuarios → todo se puede reutilizar**
4. **Si un vector no funciona → vuelve a enumerar, no lo intentes mil veces**
5. **Los errores de versión y los servicios internos son vectores frecuentemente ignorados**
6. **Siempre verificar con `id` y `whoami` después de cada intento → confirmar escalada**

---

## ORDEN DE PRIORIDAD

Sigue este orden — los más rápidos y frecuentes primero:

```
 0. foothold (www-data)  → buscar credenciales / reutilización / pivot a usuario real
 1. sudo -l              → más rápido y más común en el examen
 2. Grupos especiales    → docker/lxd = root casi seguro
 3. SUID / SGID          → find + GTFObins
 4. Capabilities         → cap_setuid = root directo
 5. Tareas Cron          → scripts escribibles o wildcard injection
 6. Password Hunting     → configs, historial, backups, DB → muy subestimado
 7. Permisos incorrectos → /etc/passwd, shadow, id_rsa de root
 8. NFS no_root_squash   → muy común en máquinas de examen
 9. Path Hijacking       → scripts que llaman sin ruta absoluta
10. Python Lib Hijacking → scripts Python con sudo o cron
11. Secuestro Biblioteca → LD_PRELOAD con sudo env_keep
12. Wildcard Injection   → tar, rsync, chown con * en cron
13. Servicios Internos   → MySQL, Redis sin auth en localhost
14. Systemd Timers       → como cron pero con systemd
15. Restricted Shell     → escapar de rbash antes de privesc
16. Docker Breakout      → si estás en contenedor
17. Kernel Exploit       → último recurso → puede crashear el sistema
```

---

## CHECKLIST RÁPIDO PARA EL EXAMEN

```
[ ] whoami && id → grupos del usuario actual
[ ] uname -a → versión del kernel → buscar CVE
[ ] sudo -l → permisos sudo → GTFObins
[ ] Subir LinPEAS → revisar output completo
[ ] find / -perm -4000 2>/dev/null → SUID → GTFObins
[ ] getcap -r / 2>/dev/null → capabilities
[ ] cat /etc/crontab + /etc/cron.* → tareas cron
[ ] pspy → monitorear procesos si cron no es evidente
[ ] ls -la /etc/passwd /etc/shadow → permisos incorrectos
[ ] find / -writable 2>/dev/null → archivos escribibles
[ ] grep -ri "password\|passwd\|secret\|key" /var/www /opt /home 2>/dev/null
[ ] ls ~/.ssh && ls /root/.ssh → claves SSH expuestas
[ ] cat ~/.bash_history → historial con credenciales
[ ] ss -tlnp → servicios internos en localhost
[ ] showmount -e <IP> → shares NFS disponibles
[ ] cat /proc/1/cgroup → ¿estoy en contenedor Docker?
[ ] ls /.dockerenv → confirmar contenedor
[ ] id → grupos especiales → docker, lxd, disk, adm, shadow
[ ] systemctl list-timers → timers de systemd activos
[ ] echo $SHELL → ¿restricted shell? → intentar escape
```

---

## CONTEXTO INICIAL — LO PRIMERO AL ENTRAR

Antes de buscar vectores, establece el contexto básico:

- ¿Quién soy? → usuario actual y grupos
- ¿Dónde estoy? → hostname, OS, versión del kernel
- ¿Qué puedo ejecutar? → sudo, SUID, capabilities
- ¿Hay restricted shell? → intentar escape primero

**→ Ver nota: 01 Reconocimiento.md**
**→ Ver nota: 02 SystemInfo.md**

---

## FASE 1 — RECONOCIMIENTO DEL SISTEMA

**Objetivo:** Entender el entorno completo antes de buscar vectores

1. OS, versión del kernel y arquitectura
   - Kernel antiguo o con CVE conocido → ver Fase 17

2. Usuario actual y grupos
   - Grupos especiales → ver Fase 2 inmediatamente

3. Variables de entorno
   - PATH modificable → Path Hijacking
   - Credenciales en variables → reutilizar

4. Historial de comandos
   - ~/.bash_history, ~/.zsh_history → comandos con credenciales

5. Archivos de configuración del shell
   - ~/.bashrc, ~/.profile → aliases, exports con credenciales

**→ Ver nota: 01 Reconocimiento.md**
**→ Ver nota: 02 SystemInfo.md**
**→ Ver nota: 03 UsersGroups.md**

---

## FASE 2 — ENUMERACIÓN AUTOMATIZADA

**Objetivo:** Mapa rápido de todos los vectores potenciales

- Subir y ejecutar LinPEAS
- Revisar output por colores: Rojo → crítico | Amarillo → revisión
- No confiar ciegamente → complementar con enumeración manual

**→ Ver nota: 04 LinPEAS.md**

---
### FASE 2.1 - ENUMERACIÓN DE CREDENCIALES  

**Objetivo:** Si se ingresa al server con el usuario www-data, lo ideal es buscar un archivo de configuración con credenciales para pivotear a un usuario real en el server. 

```bash 
# Archivos clave

- '.env'
- 'config.php'
- 'settings.py'
- 'web.config'
- 'database.yml'

# Credenciales típicas

- DB (MySQL, PostgreSQL)
- APIs
- Redis
- SMTP
```

---

## FASE 3 — SUDO

**Objetivo:** Aprovechar permisos sudo mal configurados

```
sudo -l → ¿hay entradas?
  ├── NOPASSWD + binario → GTFObins → root
  ├── sudo ALL → sudo su o sudo bash → root
  ├── Script editable → modificar → añadir reverse shell
  └── env_keep con LD_PRELOAD → Library Preload → ver Fase 13
```

Recurso clave: https://gtfobins.github.io/

**→ Ver nota: 05 Sudoers.md**

---

## FASE 4 — GRUPOS ESPECIALES

**Objetivo:** Aprovechar pertenencia a grupos con privilegios especiales

| Grupo | Vector |
|---|---|
| docker | Montar filesystem del host → acceso total |
| lxd / lxc | Montar filesystem del host → acceso total |
| disk | Acceso directo a dispositivos de bloque |
| shadow | Leer /etc/shadow → crackear hashes |
| adm | Leer logs → pueden contener credenciales |
| video | Capturar framebuffer del escritorio |
| staff | Escribir en /usr/local → plantar binarios |
| sudo | Ejecutar como root → sudo -l |

**→ Ver nota: 06 Grupos especiales.md**

---

## FASE 5 — SUID / SGID

**Objetivo:** Aprovechar binarios con bit SUID que se ejecutan como su propietario

```
find SUID → ¿binario en GTFObins?
  ├── SÍ → explotar directamente → root
  ├── Llama comandos sin ruta absoluta → Path Hijacking → ver Fase 11
  └── Binario custom → strings + ltrace + strace → analizar comportamiento
```

**→ Ver nota: 07 SUID.md**

---

## FASE 6 — CAPABILITIES

**Objetivo:** Aprovechar capabilities asignadas a binarios

| Capability | Vector |
|---|---|
| cap_setuid | Cambiar UID → root directo |
| cap_dac_override | Bypass de permisos de archivos |
| cap_sys_admin | Administración del sistema |
| cap_net_raw | Acceso a red raw |

Ejemplos comunes: python3, perl, ruby con cap_setuid → shell como root

**→ Ver nota: 08 Capabilities.md**

---

## FASE 7 — TAREAS CRON

**Objetivo:** Aprovechar tareas programadas que se ejecutan con privilegios

```
¿Hay cron como root?
  ├── Script escribible → añadir reverse shell o chmod u+s /bin/bash
  ├── Script llama binario sin ruta absoluta → Path Hijacking
  ├── Directorio del script escribible → reemplazar el script
  └── Usa wildcard (* en tar/rsync/chown) → Wildcard Injection → ver Fase 14
```

**Tip:** Si cron no aparece en enumeración estática → usar pspy para monitorear procesos

**→ Ver nota: 09 Tareas CRON.md**
**→ Ver nota: 10 CronServices.md**

---

## FASE 8 — PASSWORD HUNTING

**Objetivo:** Encontrar credenciales en el sistema para reutilizarlas o escalar

Lugares a revisar sistemáticamente:
```
/var/www/html/          → config.php, .env, database.yml, wp-config.php
/opt/                   → aplicaciones de terceros con configs
/home/*/                → archivos personales, .ssh, historial
/etc/                   → configs de servicios con credenciales
/var/backups/           → backups de configuraciones
/tmp/ y /var/tmp/       → archivos temporales con datos sensibles
Bases de datos locales  → sqlite, mysql con credenciales de otros usuarios
Variables de entorno    → env → tokens, passwords, API keys
Historial de comandos   → ~/.bash_history → comandos con -p password
Archivos .env           → tokens y credenciales de aplicaciones modernas
```

Credencial encontrada → probar inmediatamente en:
- `su <usuario>` con esa contraseña
- SSH al localhost o a otros hosts de la red
- Servicios internos (MySQL, Redis, etc.)

**→ Ver nota: 11 Password Hunting.md**

---

## FASE 9 — PERMISOS INCORRECTOS EN ARCHIVOS

**Objetivo:** Aprovechar archivos críticos con permisos débiles

```
/etc/passwd escribible   → añadir usuario root personalizado → root directo
/etc/shadow legible      → extraer hashes → crackear offline
/etc/sudoers escribible  → añadir NOPASSWD para mi usuario
/root/.ssh/id_rsa legible → conectar como root por SSH
Claves SSH de otros usuarios legibles → moverse lateralmente
```

**→ Ver nota: 12 Permisos incorrectos.md**

---

## FASE 10 — NFS con no_root_squash

**Objetivo:** Aprovechar shares NFS mal configurados para crear binarios SUID

Condición necesaria: share NFS con opción `no_root_squash` en /etc/exports

```
Desde el atacante como root:
  1. Montar el share NFS
  2. Crear binario SUID (copia de bash o binario malicioso)
  3. Desde la víctima → ejecutar el binario → root
```

Muy común en máquinas de examen → revisar siempre si hay NFS abierto

**→ Ver nota: 13 NFS no_root_squash.md**

---

## FASE 11 — PATH HIJACKING

**Objetivo:** Plantar un binario malicioso en el PATH antes que el legítimo

Condiciones necesarias:
- Script o binario con privilegios llama comando sin ruta absoluta
- Puedo escribir en un directorio que está antes en el PATH

```
Script cron llama "backup" sin /usr/bin/backup
  → Crear "backup" malicioso en directorio escribible
  → Añadir ese directorio al PATH → root cuando ejecute cron
```

**→ Ver nota: 14 Path Hijacking.md**

---

## FASE 12 — PYTHON LIBRARY HIJACKING

**Objetivo:** Plantar librería Python maliciosa que sea importada por script privilegiado

Condiciones necesarias:
- Script Python con privilegios (sudo, cron, SUID)
- Directorio escribible antes que la librería real en sys.path

```
Script con sudo → import requests
  → Crear requests.py malicioso en el mismo directorio
  → Al ejecutarse → carga tu requests.py → root
```

**→ Ver nota: 15 Python Library Hijacking.md**

---

## FASE 13 — SECUESTRO DE BIBLIOTECA (LD_PRELOAD / RPATH)

**Objetivo:** Cargar librería maliciosa antes que las legítimas del sistema

Escenarios comunes:
- sudo con env_keep+=LD_PRELOAD → compilar .so malicioso → root
- Binario SUID con RPATH a directorio escribible → plantar .so
- Binario busca .so en directorio escribible primero

**→ Ver nota: 16 Secuestro de Biblioteca.md**

---

## FASE 14 — WILDCARD INJECTION

**Objetivo:** Aprovechar comandos con wildcard (*) en cron que procesan nombres de archivos

Comandos vulnerables comunes: tar, rsync, chown, chmod

```
Cron ejecuta: tar -czf backup.tar.gz /home/user/*
  → Crear archivos con nombres de flags de tar:
     touch -- "--checkpoint=1"
     touch -- "--checkpoint-action=exec=bash shell.sh"
  → Cuando cron ejecuta → interpreta tus archivos como flags → RCE como root
```

**→ Ver nota: 17 Wildcard Injection.md**

---

## FASE 15 — SERVICIOS INTERNOS

**Objetivo:** Aprovechar servicios en localhost con versiones vulnerables o sin autenticación

```
ss -tlnp → ¿servicios en 127.0.0.1?
  ├── MySQL/MariaDB → credenciales en config → UDF para RCE
  ├── Redis sin auth → escritura de archivos como root
  ├── Memcached → datos sensibles en memoria
  └── Servicio web interno → explotar versión vulnerable
```

**→ Ver nota: 18 Servicios Internos.md**

---

## FASE 16 — SYSTEMD TIMERS

**Objetivo:** Aprovechar timers de systemd que ejecutan scripts con privilegios

Similar a cron pero con systemd:
```
systemctl list-timers → ¿timers activos?
  ├── Script del timer escribible → modificar → root
  ├── Archivo .service o .timer escribible → reemplazar
  └── Directorio escribible → plantar script malicioso
```

**→ Ver nota: 19 Systemd Timers.md**

---

## FASE 17 — RESTRICTED SHELL ESCAPE

**Objetivo:** Escapar de un shell restringido (rbash, lshell, etc.) antes de privesc

Indicios de restricted shell:
- Comandos con / no permitidos
- cd bloqueado
- Variables de entorno bloqueadas

Técnicas comunes de escape:
```
vi/vim → :!/bin/bash
python → import os; os.system('/bin/bash')
awk → awk 'BEGIN {system("/bin/bash")}'
SSH → ssh user@host -t "/bin/bash --noprofile"
Copiar bash a directorio permitido
```

**→ Ver nota: 20 Restricted Shell Escape.md**

---

## FASE 18 — BINARIOS ESPECÍFICOS

**Objetivo:** Identificar binarios instalados con técnicas conocidas de escalada

- Binarios no estándar instalados → buscar en GTFObins
- Lenguajes de scripting con SUID (python, perl, ruby, lua)
- Herramientas de administración mal configuradas

Recursos:
- GTFObins → https://gtfobins.github.io/
- LOLBAS → https://lolbas-project.github.io/

**→ Ver nota: 21 Binarios Especificos.md**

---

## FASE 19 — DOCKER / CONTAINER BREAKOUT

**Objetivo:** Escapar de un contenedor Docker al host

Indicios de estar en contenedor:
```
ls /.dockerenv          → existe → estás en Docker
cat /proc/1/cgroup      → contiene "docker"
hostname                → hash corto
```

Vectores de escape:
```
  ├── Grupo docker → montar filesystem del host
  ├── /var/run/docker.sock accesible → crear contenedor privilegiado
  ├── --privileged → montar dispositivos del host
  └── CAP_SYS_ADMIN en el contenedor
```

**→ Ver nota: 22 Docker Breakout.md**

---

## FASE 20 — KERNEL EXPLOIT

**Objetivo:** Explotar vulnerabilidades del kernel — ÚLTIMO RECURSO

⚠️ Puede crashear el sistema → usar solo si no hay otra opción

CVEs frecuentes en el examen:

| CVE | Nombre | Versión afectada |
|---|---|---|
| CVE-2021-4034 | PwnKit | pkexec en casi todos → siempre intentar |
| CVE-2022-0847 | DirtyPipe | Kernel 5.8 - 5.16.11 |
| CVE-2021-3156 | Baron Samedit | sudo < 1.9.5p2 |
| CVE-2016-5195 | DirtyCow | Kernel < 4.8.3 |
| CVE-2023-2640 | GameOver(lay) | Ubuntu kernels específicos |

**→ Ver nota: 23 Kernel.md**

---

## FLUJO DE DECISIÓN RÁPIDO

```
Shell como usuario no privilegiado
│
├── echo $SHELL → ¿restricted? → escape primero → Fase 17
│
├── id → ¿grupos especiales?
│     └── docker/lxd → breakout → root inmediato
│
├── sudo -l → ¿entradas?
│     ├── NOPASSWD + binario → GTFObins → root
│     └── Script editable → modificar → root
│
├── find SUID → GTFObins → root
│
├── LinPEAS → revisar rojo/amarillo
│     ├── Capabilities → cap_setuid → root
│     ├── Cron escribible → shell en script
│     ├── Wildcard en cron → Wildcard Injection
│     ├── Permisos → /etc/passwd → root
│     └── Credenciales → reutilizar → su / SSH
│
├── showmount → NFS con no_root_squash → root
│
├── ss -tlnp → servicios internos → MySQL/Redis
│
├── cat /proc/1/cgroup → ¿Docker? → socket escape
│
└── uname -a → CVE conocido → PwnKit → root
```

---

## REFERENCIAS A NOTAS ESPECÍFICAS

```
01 Reconocimiento.md           → Enumeración inicial
02 SystemInfo.md               → Info del sistema y kernel
03 UsersGroups.md              → Usuarios y grupos
04 LinPEAS.md                  → Automatización
05 Sudoers.md                  → Escalada vía sudo
06 Grupos especiales.md        → Escalada vía grupos
07 SUID.md                     → Escalada vía SUID/SGID
08 Capabilities.md             → Escalada vía capabilities
09 Tareas CRON.md              → Escalada vía cron
10 CronServices.md             → Servicios y automatización
11 Password Hunting.md         → Búsqueda de credenciales
12 Permisos incorrectos.md     → Archivos con permisos débiles
13 NFS no_root_squash.md       → Escalada vía NFS
14 Path Hijacking.md           → Escalada vía PATH
15 Python Library Hijacking.md → Escalada vía librerías Python
16 Secuestro de Biblioteca.md  → LD_PRELOAD y RPATH
17 Wildcard Injection.md       → Escalada vía wildcards
18 Servicios Internos.md       → Servicios en localhost
19 Systemd Timers.md           → Escalada vía timers
20 Restricted Shell Escape.md  → Escape de shells restringidos
21 Binarios Especificos.md     → GTFObins y binarios custom
22 Docker Breakout.md          → Escape de contenedores
23 Kernel.md                   → Kernel exploits
```
