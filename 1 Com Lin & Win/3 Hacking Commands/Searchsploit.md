# Searchsploit

Tags: #Enumeracion #SearchSploit #ExploitDB #Compilacion

- [Exploit-DB](https://www.exploit-db.com/)
- [Rapid7](https://www.rapid7.com/db/)
- [ExploitDB Binarios compilados](https://gitlab.com/exploit-database/exploitdb-bin-sploits/-/tree/main/bin-sploits)

## OBJETIVO

- Buscar exploits localmente en la base de datos de Exploit-DB
- Examinar y descargar exploits para modificarlos
- Compilar exploits en C para Windows o Linux

## TIPS

1. **Buscar por servicio + versión exacta → más resultados relevantes**
2. **-x antes de -m → examinar siempre el exploit antes de descargarlo**
3. **-w → obtener la URL → ver el exploit en el navegador con más detalle**
4. **Exploits en C → compilar según el OS objetivo → mingw para Windows, gcc para Linux**
5. **Si no encuentra nada → buscar en exploit-db.com o rapid7.com directamente**

---

## 1. BÚSQUEDA DE EXPLOITS

```bash
❯ searchsploit <exploit_name>
# Buscar exploits por nombre de servicio o versión
# Ejemplo: searchsploit vsftpd 2.3.4

❯ searchsploit <servicio> <version>
# Búsqueda por servicio y versión específica
# Ejemplo: searchsploit apache 2.4.49

❯ searchsploit -t <exploit_name>
# -t → buscar solo en el título del exploit → resultados más precisos

❯ searchsploit -e "exploit_name"
# Búsqueda exacta → entre comillas

❯ searchsploit -c <exploit_name>
# -c → Case Sensitive → distingue mayúsculas y minúsculas

❯ searchsploit <exploit_name> -w
# -w → mostrar URL del exploit → abrir en navegador para más detalle

❯ searchsploit <exploit_name> --exclude="dos"
# Excluir resultados con cierta palabra → filtrar DoS si buscas RCE
```

---

## 2. EXAMINAR Y DESCARGAR EXPLOITS

```bash
❯ searchsploit -x <exploit_path>
# -x → examinar el contenido del exploit sin descargarlo
# Ejemplo: searchsploit -x exploits/linux/remote/17491.py

❯ searchsploit -m <exploit_path>
# -m → copiar/descargar el exploit al directorio actual
# Ejemplo: searchsploit -m exploits/linux/remote/17491.py
```

### Insight

- Siempre -x antes de -m → entender qué hace el exploit antes de descargarlo
- Revisar variables a modificar: RHOST, LHOST, LPORT, usuario, contraseña
- Primeros comentarios del exploit suelen tener instrucciones de uso y compilación

---

## 3. ACTUALIZACIÓN DE LA BASE DE DATOS

```bash
❯ searchsploit -u
# Actualizar la base de datos local de exploits
# Ejecutar periódicamente para tener los exploits más recientes
```

---

## 4. COMPILAR EXPLOITS EN C

### Instalar compiladores

```bash
❯ apt install mingw-w64
# Compilador cruzado → compilar ejecutables Windows desde Linux

❯ apt install gcc
# Compilador C/C++ para Linux
```

### Compilar para Windows (desde Linux)

```bash
❯ i686-w64-mingw32-gcc <exploit.c> -o exploit.exe
# Compilar para Windows 32 bits → -o → nombre del output

❯ i686-w64-mingw32-gcc <exploit.c> -o exploit.exe -lws2_32
# -lws2_32 → incluir librería de sockets Windows
# Necesario en exploits que usan conexiones de red

❯ x86_64-w64-mingw32-gcc <exploit.c> -o exploit.exe
# Compilar para Windows 64 bits

❯ x86_64-w64-mingw32-gcc <exploit.c> -o exploit.exe -lws2_32
# Windows 64 bits con sockets de red
```

### Compilar para Linux (desde Linux)

```bash
❯ gcc -pthread <exploit.c> -o exploit -lcrypt
# -pthread → soporte para hilos | -lcrypt → librería de cifrado
# Necesario en exploits como DirtyCow

❯ gcc <exploit.c> -o exploit
# Compilación básica para Linux

❯ gcc -m32 <exploit.c> -o exploit
# -m32 → compilar para arquitectura 32 bits en sistema 64 bits
```

### Insight

- Si da errores de compilación → revisar includes y librerías requeridas en los comentarios
- Binarios precompilados para Windows → buscar en el repositorio exploitdb-bin-sploits

---

## 5. FLUJO DE USO EN EL EXAMEN

```bash
# 1. Identificar versión del servicio
❯ nmap -sCV -p <puerto> <IP>

# 2. Buscar exploits
❯ searchsploit <servicio> <version>

# 3. Examinar el más prometedor
❯ searchsploit -x <ruta_del_exploit>

# 4. Descargar y modificar
❯ searchsploit -m <ruta_del_exploit>
❯ nano exploit.py
# Cambiar: IP, puerto, usuario, contraseña según el objetivo

# 5. Compilar si es en C
❯ gcc exploit.c -o exploit                              # Linux
❯ i686-w64-mingw32-gcc exploit.c -o exploit.exe         # Windows 32 bits
❯ x86_64-w64-mingw32-gcc exploit.c -o exploit.exe       # Windows 64 bits

# 6. Ejecutar
❯ python3 exploit.py <IP> <PUERTO>
❯ ./exploit
```

---

## ONE-LINERS MENTALES

- Versión identificada → searchsploit inmediato antes de buscar en Google
- Muchos resultados → agregar -t para buscar solo en el título
- Exploit en C con red → mingw + -lws2_32 para Windows
- Exploit en C con hilos → gcc -pthread + -lcrypt para Linux
- No encuentras nada local → exploit-db.com y rapid7.com directamente