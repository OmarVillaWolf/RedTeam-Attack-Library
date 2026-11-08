# MSSQL 

Tags: #MSSQL 

MySQL es un sistema para manejar bases de datos relacionales. Guarda la información en tablas con filas y columnas y usa el idioma SQL. Es de código abierto y sirve para crear páginas web, tiendas en línea y guardar datos de usuarios.

```powershell
# ARCHIVOS DENTRO DEL SERVER

C:\SQL2019\ExpressAdv_ENU\
	- sql-Configuration.INI    # Contiene credenciales 

C:\SQLServer\Logs
	- ERRORLOG.BAK    # Contiene credenciales en los logs 
```

```powershell 
# Archivos de configuraciones 
❯ Get-ChildItem C:\inetpub -Recurse -Force -Include *.config,*.xml,*.json,*.ini,*.txt -ErrorAction SilentlyContinue | Select FullName

❯ Select-String -Path C:\inetpub\,C:\Temp\ -Pattern "connectionString|Password|pwd|Data Source" -ErrorAction SilentlyContinue

# Instancias y cadenas de conexión del registro 
❯ Get-ChildItem "HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server" -ErrorAction SilentlyContinue | Select Name

❯ Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\Instance Names\SQL" -ErrorAction SilentlyContinue

# Archivos SQL server 
❯ Get-ChildItem "C:\Program Files\Microsoft SQL Server" -Recurse -Include *.ini,*.xml,*.config -ErrorAction SilentlyContinue | Select FullName

# Historial y archivos 
❯ Get-Content (Get-PSReadLineOption).HistorySavePath -ErrorAction SilentlyContinue
❯ Get-ChildItem C:\Users\dom -Recurse -Force -Include *.kdbx,*.xml,*.txt,*.ps1,*.config -ErrorAction SilentlyContinue | Select FullName
```