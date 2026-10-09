# Reconocimiento & Búsqueda de Credenciales

**Tags:** #PrivEsc #Enumeration #Recon #Credentials

## 🖥️ SERVICIOS E INTERNALS

### Interfaces de Red & Hosts

```bash
❯ ip a                          # Conocer las interfaces de red
❯ cat /etc/hosts                # Conocer los hostnames internos
```

### Usuarios Activos & Último Acceso

```bash
❯ lastlog                       # Último inicio de sesión de los usuarios
	Username Port From Latest
	cliff.moore pts/0 127.0.0.1 Tue Aug 2 19:32:29 +0000 2022
	backupsvc 'Never logged in'

❯ w                             # Usuarios con sesión iniciada
	USER TTY FROM LOGIN@ IDLE JCPU PCPU WHAT
	cliff.mo pts/0 10.10.14.16 Tue19 40:54m 0.02s 0.02s -bash
```

### Shell & Historial de Comandos

```bash
❯ echo "$SHELL"                 # Mirar la shell

❯ history                       # Historial de comandos
❯ cat ~/.bash_history
❯ cat ~/.zsh_history
❯ cat ~/.sh_history

❯ find / -type f \( -name *_hist -o -name *_history \) -exec ls -l {} \; 2>/dev/null
# Búsqueda de archivos de historial
```

### Directorios Ocultos del Usuario

```bash
❯ ls -la /home/usuario/         # Mirar los directorios ocultos del usuario

	.ssh/                       # Buscar la llave privada
	.bash_history               # Mirar el historial de comandos
	.config/
	.local/
	.hidden/                    # A veces hay credenciales en .bak
```

### Paquetes Instalados & Binarios

```bash
❯ apt list --installed | tr "/" " " | cut -d" " -f1,3 | sed 's/[0-9]://g' | tee -a installed_pkgs.list
# Mirar los paquetes instalados

❯ sudo -V                       # Mirar las versiones de sudo

❯ ls -l /bin /usr/bin/ /usr/sbin/
# Mirar los binarios instalados
```

### Verificar Binarios contra GTFObins

```bash
❯ for i in $(curl -s https://gtfobins.org/api.json | jq -r '.executables | keys[]'); do if grep -q "$i" installed_pkgs.list; then echo "Check for GTFO: $i";fi; done
```

### Archivos de Configuración & Scripts

```bash
❯ find / -type f \( -name *.conf -o -name *.config \) -exec ls -l {} \; 2>/dev/null
# Buscar archivos de configuración

❯ find / -type f -name "*.sh" 2>/dev/null | grep -v "src\|snap\|share"
# Buscar scripts
```

---

## 🔑 BÚSQUEDA DE CREDENCIALES

! **Importante:** Al enumerar un sistema, anotar cualquier credencial. Pueden estar en:
- Archivos de configuración (`.conf`, `.config`, `.xml`)
- Scripts de shell
- Historial de bash del usuario
- Archivos de respaldo (`.bak`)
- Bases de datos
- Archivos de texto
- Memoria de procesos

### Escalada Rápida (si encuentras credenciales)

```bash
❯ su - root          # Cambiar al usuario 'root' si se encuentran credenciales
❯ ssh root@IP        # Ingresar por SSH con el usuario root si se tienen credenciales
```

---

## 🔓 BUSCAR EN SSH/AUTENTICACIÓN

```bash
❯ ls ~/.ssh                     # Listar las claves SSH

# Ejemplos:
	id_rsa
	id_rsa.pub
	known_hosts

# Claves SSH de todos los usuarios:
❯ find / -type f -name "id_rsa" -o -name "id_ed25519" 2>/dev/null

# Archivos autorizados:
❯ find / -type f -name "authorized_keys" 2>/dev/null

# SSH config con credenciales:
❯ cat ~/.ssh/config 2>/dev/null | grep -i "user\|password\|identityfile"
```

---

## 📜 BUSCAR EN HISTORIALES DE BASH/SHELL

```bash
# Historial del usuario actual (← IMPORTANTE)
❯ cat ~/.bash_history | grep -i "password\|mysql\|ssh\|curl.*-u"

# Historial de otros usuarios (si eres root):
❯ cat /home/*/.bash_history 2>/dev/null | grep -i "password\|mysql"

# Historial de root:
❯ cat /root/.bash_history 2>/dev/null | grep -i "password"
```

---

## 🗄️ BUSCAR EN BASES DE DATOS

```bash
# Si MySQL está corriendo:
❯ mysql -u root -e "SELECT User, Host FROM mysql.user;" 2>/dev/null

# Si tienes credenciales:
❯ mysql -u wordpressuser -p'WPadmin123!' -e "show databases;" 2>/dev/null

# SQLite:
❯ find / -name "*.db" -o -name "*.sqlite" 2>/dev/null | xargs strings | grep -i "password\|admin"
```

---

## 🧠 BUSCAR CREDENCIALES EN MEMORIA

```bash
# Si tienes acceso root:
❯ strings /proc/[PID]/mem | grep -i "password" 2>/dev/null

# O busca procesos con credenciales:
❯ ps aux | grep -E "mysql|postgres|ssh|curl.*-u" | grep -v grep
```

---

## ⚙️ BUSCAR EN ARCHIVOS DE CONFIGURACIÓN

```bash
# Busca en todos los archivos de config (más específico):
❯ find / -type f \( -name "*.php" -o -name "*.conf" -o -name "*.config" -o -name "*config*" \) 2>/dev/null | xargs grep -l "password\|user\|pass\|pwd\|credential" 2>/dev/null | head -20
```

---

## 🌐 BUSCAR EN ARCHIVOS DE APLICACIONES WEB

```bash
# WordPress, Drupal, Joomla, etc:
❯ grep -r "DB_PASSWORD\|db_password\|database_password" /var/www* 2>/dev/null

# Laravel:
❯ grep -r "DB_PASSWORD\|DB_USERNAME" /var/www* 2>/dev/null

# Aplicaciones genéricas:
❯ find /var/www -type f \( -name "*.php" -o -name "*.py" -o -name "*.conf" \) -exec grep -l "password\|credentials\|secret" {} \; 2>/dev/null
```

---

## 📄 BUSCAR EN ARCHIVOS DE CREDENCIALES

```bash
# Busca archivos típicos de credenciales:
❯ find / -type f \( -name ".htpasswd" -o -name ".env" -o -name ".env.local" -o -name "credentials" -o -name "*creds*" \) 2>/dev/null

# .env files (MUY COMÚN):
❯ find / -name ".env" -o -name ".env.local" -o -name ".env.production" 2>/dev/null | xargs cat 2>/dev/null
```

---

## 🌍 BUSCAR EN VARIABLES DE ENTORNO

```bash
# Variables de entorno del usuario:
❯ env | grep -i "password\|api\|secret\|token\|user"

# Si eres root, ve las de otros procesos:
❯ cat /proc/*/environ | tr '\0' '\n' | grep -i "password\|api\|secret" 2>/dev/null
```

---

## ⚡ ONE-LINER POTENTE

```bash
# Busca CUALQUIER cosa que parezca credencial:
❯ grep -r "password\|passwd\|pwd\|secret\|token\|api_key\|apikey\|Authorization.*Bearer\|Basic.*=" /var/www /etc /home /opt /srv 2>/dev/null | grep -v "^Binary" | head -50
```

---

## ⚠️ FINDINGS COMUNES

! **Si encuentras `.env`** → Contendrá DB_PASSWORD, API_KEYS, SECRETS
! **Si encuentras `.bash_history`** → Buscar comandos con: mysql, ssh, curl -u, psql -U
! **Si encuentras claves SSH** → Intentar conectar a hosts listados en `known_hosts`
! **Si encuentras SUID binario custom** → Revisar con `strings` y `ltrace` para vulnerabilidades
! **Si tienes acceso a `/proc/*/mem`** → Buscar credenciales en memoria de procesos
! **Si `/etc/shadow` es legible** → Crackear hashes con `john` o `hashcat`
