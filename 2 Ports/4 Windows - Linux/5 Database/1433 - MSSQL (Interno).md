# Microsoft SQL Server (1433)

Tags: #MSSQL #SQLServer #Windows

# DESDE DENTRO DEL SERVIDOR (ACCESO LOCAL)

## 1. CONECTARSE AL MSSQL LOCAL SIN CREDENCIALES

```bash
# NT Authority\System puede conectarse al MSSQL local sin contraseña
# Porque SYSTEM es el dueño del servicio → autenticación implícita

# Verificar si MSSQL está corriendo localmente
❯ netstat -ano | findstr 1433
❯ ss -tlnp | grep 1433
❯ Get-Service | Where-Object {$_.Name -like "*sql*"}
❯ sc query MSSQLSERVER
❯ sc query "MSSQL$INSTANCE"    # Si tiene nombre de instancia
```

### Desde CMD como SYSTEM o Admin local

```bash
# Conexión con sqlcmd → herramienta nativa de SQL Server
❯ sqlcmd -S localhost -Q "SELECT SYSTEM_USER"
# Sin credenciales → usa autenticación Windows del usuario actual
# Si eres SYSTEM → autenticas como SA implícitamente

❯ sqlcmd -S localhost -Q "SELECT IS_SRVROLEMEMBER('sysadmin')"
# 1 → soy sysadmin desde SYSTEM

❯ sqlcmd -S localhost -Q "EXEC xp_cmdshell 'whoami'"
# RCE desde SYSTEM → sin credenciales externas

# Con nombre de instancia
❯ sqlcmd -S localhost\SQLEXPRESS -Q "SELECT SYSTEM_USER"
❯ sqlcmd -S .\SQLEXPRESS -Q "SELECT SYSTEM_USER"

# Listar instancias disponibles
❯ sqlcmd -L
```

### Desde PowerShell como SYSTEM o Admin local

```powershell
# Sin credenciales → autenticación Windows implícita
❯ Invoke-Sqlcmd -Query "SELECT SYSTEM_USER" -ServerInstance localhost
❯ Invoke-Sqlcmd -Query "SELECT IS_SRVROLEMEMBER('sysadmin')" -ServerInstance localhost
❯ Invoke-Sqlcmd -Query "EXEC xp_cmdshell 'whoami'" -ServerInstance localhost

# Si el módulo no está disponible → cargar manualmente
❯ Import-Module SqlServer
❯ Invoke-Sqlcmd -Query "SELECT SYSTEM_USER" -ServerInstance "localhost"

# Alternativa con .NET directamente
❯ $conn = New-Object System.Data.SqlClient.SqlConnection
❯ $conn.ConnectionString = "Server=localhost;Integrated Security=True"
❯ $conn.Open()
❯ $cmd = $conn.CreateCommand()
❯ $cmd.CommandText = "SELECT SYSTEM_USER"
❯ $cmd.ExecuteScalar()
```

## 2. VERIFICAR EL USUARIO DEL SERVICIO SQL (DESDE DENTRO)

```bash
# Saber con qué usuario corre el servicio MSSQL
# Importante porque ese usuario puede tener SeImpersonatePrivilege

❯ sc qc MSSQLSERVER
# Ver "SERVICE_START_NAME" → usuario del servicio

❯ Get-WmiObject Win32_Service | Where-Object {$_.Name -like "*sql*"} | Select Name,StartName
# PowerShell → usuario del servicio SQL

❯ tasklist /v | findstr /i "sql"
# Ver procesos SQL y el usuario que los ejecuta

# Usuarios típicos del servicio MSSQL:
# NT Service\MSSQLSERVER   → tiene SeImpersonatePrivilege → Potato attack
# NT Authority\System      → SYSTEM → acceso total
# NT Authority\Network Service → tiene SeImpersonatePrivilege
# DOMAIN\ServiceAccount    → cuenta de dominio → credenciales valiosas
```

## 3. ENUMERAR DESDE DENTRO CON SQLCMD

```bash
# Enumeración completa desde shell local como SYSTEM o admin

❯ sqlcmd -S localhost -Q "SELECT name FROM master.dbo.sysdatabases"
# Todas las bases de datos

❯ sqlcmd -S localhost -Q "SELECT name, type_desc FROM sys.server_principals WHERE type IN ('U','G','S')"
# Todos los logins → usuarios Windows, grupos y SQL

❯ sqlcmd -S localhost -Q "SELECT * FROM sys.server_permissions WHERE permission_name='IMPERSONATE'"
# Ver impersonación disponible desde dentro

❯ sqlcmd -S localhost -Q "SELECT * FROM sys.servers"
# Linked servers → otros SQL servers enlazados

❯ sqlcmd -S localhost -Q "SELECT name FROM sys.databases"
# Bases de datos disponibles

❯ sqlcmd -S localhost -Q "USE ❮database❯; SELECT * FROM information_schema.tables"
# Tablas de una base de datos específica
```

