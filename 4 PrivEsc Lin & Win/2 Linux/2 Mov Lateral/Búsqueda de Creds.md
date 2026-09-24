# Búsqueda de credenciales 

Tags: #PrivEsc 

Al enumerar un sistema, es importante anotar cualquier credencial. Estas se pueden encontrar en archivos de configuración (`.conf`, `.config`, `.xml`, etc.), scripts de shell, el archivo de historial de bash de un usuario, archivos de respaldo (`.bak`), dentro de archivos de bases de datos o incluso en archivos de texto. Las credenciales pueden ser útiles para escalar a otros usuarios o incluso a root, acceder a bases de datos y a otros sistemas dentro del entorno.

## ONE LINER POTENTE 
```bash 
# Busca CUALQUIER cosa que parezca credencial:
❯ grep -r "password\|passwd\|pwd\|secret\|token\|api_key\|apikey\|Authorization.*Bearer\|Basic.*=" \
  /var/www /etc /home /opt /srv 2>/dev/null | \
  grep -v "^Binary" | head -50
```

## BUSCAR EN SSH/AUTENTICACIÓN
```bash 
❯ ls ~/.ssh      # Listar las claves SSH 

# Ejemplos: 
	id_rsa 
	id_rsa.pub 
	known_hosts

# Claves SSH de todos los usuarios:
find / -type f -name "id_rsa" -o -name "id_ed25519" 2>/dev/null

# Archivos autorizados:
find / -type f -name "authorized_keys" 2>/dev/null

# SSH config con credenciales:
cat ~/.ssh/config 2>/dev/null | grep -i "user\|password\|identityfile"
```

## BUSCAR EN HISTORIALES DE BASH/SHELL
```bash 
# Historial del usuario actual
❯ cat ~/.bash_history | grep -i "password\|mysql\|ssh\|curl.*-u"

# Historial de otros usuarios (si eres root):
❯ cat /home/*/.bash_history 2>/dev/null | grep -i "password\|mysql"

# Historial de root:
❯ cat /root/.bash_history 2>/dev/null | grep -i "password"
```

## BUSCAR EN BASES DE DATOS
```bash 
# Si MySQL está corriendo:
mysql -u root -e "SELECT User, Host FROM mysql.user;" 2>/dev/null

# Si tienes credenciales:
mysql -u wordpressuser -p'WPadmin123!' -e "show databases;" 2>/dev/null

# SQLite:
find / -name "*.db" -o -name "*.sqlite" 2>/dev/null | xargs strings | grep -i "password\|admin"
```

## BUSCAR CREDENCIALES EN MEMORIA
```bash 
# Si tienes acceso root:
❯ strings /proc/[PID]/mem | grep -i "password" 2>/dev/null

# O busca procesos con credenciales:
❯ ps aux | grep -E "mysql|postgres|ssh|curl.*-u" | grep -v grep
```

## Buscar credenciales en archivos de configuración 
```bash 
# Busca en todos los archivos de config (más específico)
❯ find / -type f \( -name "*.php" -o -name "*.conf" -o -name "*.config" -o -name "*config*" \) 2>/dev/null | \
  xargs grep -l "password\|user\|pass\|pwd\|credential" 2>/dev/null | head -20

# O más agresivo:
❯ find / -type f 2>/dev/null | xargs grep -h "password.*=\|DB_PASSWORD\|MYSQL_PASSWORD" 2>/dev/null | grep -v "^#"
```

## BUSCAR EN ARCHIVOS DE APLICACIONES WEB
```bash 
# WordPress, Drupal, Joomla, etc:
❯ grep -r "DB_PASSWORD\|db_password\|database_password" /var/www* 2>/dev/null

# Laravel:
❯ grep -r "DB_PASSWORD\|DB_USERNAME" /var/www* 2>/dev/null

# Aplicaciones genéricas:
❯ find /var/www -type f \( -name "*.php" -o -name "*.py" -o -name "*.conf" \) -exec grep -l "password\|credentials\|secret" {} \; 2>/dev/null
```

## BUSCAR EN ARCHIVOS DE CREDENCIALES
```bash 
# Busca archivos típicos de credenciales:
❯ find / -type f \( -name ".htpasswd" -o -name ".env" -o -name ".env.local" -o -name "credentials" -o -name "*creds*" \) 2>/dev/null

# .env files (muy común):
❯ find / -name ".env" -o -name ".env.local" -o -name ".env.production" 2>/dev/null | xargs cat 2>/dev/null
```

## BUSCAR EN VARIABLES DE ENTORNO (si eres el usuario)
```bash 
# Variables de entorno del usuario:
❯ env | grep -i "password\|api\|secret\|token\|user"

# Si eres root, ve las de otros procesos:
❯ cat /proc/*/environ | tr '\0' '\n' | grep -i "password\|api\|secret" 2>/dev/null
```