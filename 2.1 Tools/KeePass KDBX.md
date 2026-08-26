# KeePass 

Tags: #KeePass #PostExplotacion #FileAnalysis #PasswordCracking #KDBX

## OBJETIVO
- Extraer el hash de un archivo .kdbx para crackearlo offline
- Obtener las contraseñas almacenadas dentro del gestor
- Acceder al vault cuando se tiene el archivo pero no la contraseña maestra

## TIPS
1. **El archivo .kdbx puede estar en shares SMB, escritorios, carpetas de usuario → búscalo siempre**
2. **Puede requerir solo contraseña maestra, solo archivo clave, o ambos → identificar antes de crackear**
3. **Si tienes el archivo clave (.key, .jpg, .png, etc.) → necesitas ambos para crackear**
4. **hashcat es más rápido que john para crackear si tienes GPU**
5. **El vault puede contener contraseñas de DA, servicios, infraestructura → alto valor**

## TOOLS
* [KeePassXC](https://keepassxc.org/)
* [keepass2john](https://github.com/openwall/john)
* [John the Ripper](https://github.com/openwall/john)
* [Hashcat](https://hashcat.net/hashcat/)

---

## 1. ABRIR EL ARCHIVO DIRECTAMENTE

```bash
❯ keepassxc file.kdbx
# Abre KeePass gráficamente → útil si ya tienes la contraseña maestra
# Pide contraseña maestra al abrir → probar contraseñas encontradas previamente
```

### Insight
- Antes de crackear → probar contraseñas ya encontradas en la máquina
- Contraseñas comunes de primer intento: nombre del usuario, nombre de la empresa,
  hostname de la máquina, contraseñas encontradas en otros archivos

---

## 2. CRACKEAR SIN ARCHIVO CLAVE (SOLO CONTRASEÑA MAESTRA)

```bash
❯ keepass2john file.kdbx > hash.txt
# No requiere creds → extrae el hash del archivo .kdbx
# El hash tiene formato $keepass$*2*...

❯ john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
# Crackear con John → más simple de usar

❯ hashcat -m 13400 hash.txt /usr/share/wordlists/rockyou.txt
# Crackear con Hashcat → más rápido con GPU
# Modo 13400 → KeePass

❯ hashcat -m 13400 hash.txt /usr/share/wordlists/rockyou.txt \
  -r /usr/share/hashcat/rules/best64.rule
# Con reglas → más probabilidad de crackear
```

* [Keepass4brute](https://github.com/r3nt0n/keepass4brute)
```bash 
# KeePass formato '40000' 
❯ ./keepass4brute.sh <kdbx-file> /usr/share/wordlists/rockyou.txt
```

---

## 3. CRACKEAR CON ARCHIVO CLAVE

```bash
# El archivo clave puede ser cualquier tipo: .key, .jpg, .png, .xml, etc.
# Buscar archivos sospechosos en el mismo directorio que el .kdbx

❯ keepass2john -k archivo_clave.jpg file.kdbx > hash.txt
# -k → especificar el archivo clave
# Extrae hash combinando .kdbx + archivo clave

❯ john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
# Crackear el hash resultante

❯ hashcat -m 13400 hash.txt /usr/share/wordlists/rockyou.txt
# Alternativa con hashcat
```

### Insight
- Si el .kdbx requiere archivo clave y no tienes la contraseña → el hash que extraes
  ya incorpora el archivo clave → crackear solo la parte de la contraseña maestra
- Archivo clave solo (sin contraseña) → keepass2john -k archivo.key file.kdbx
  y john puede encontrar la contraseña vacía directamente

---

## 4. DÓNDE BUSCAR ARCHIVOS .kdbx EN LA MÁQUINA

```bash
# Desde shell en Windows
❯ dir /s /b *.kdbx 2>nul
# Buscar en todo el disco

❯ dir /s /b *.key 2>nul
# Buscar archivos clave asociados

# Desde Linux (shell con acceso al sistema de archivos)
❯ find / -name "*.kdbx" 2>/dev/null
❯ find / -name "*.key" 2>/dev/null

# En shares SMB montados
❯ find /tmp/mnt -name "*.kdbx" 2>/dev/null
```

### Ubicaciones frecuentes
```
C:\Users\<user>\Desktop\
C:\Users\<user>\Documents\
C:\Users\<user>\AppData\Roaming\KeePass\
Shares de red → carpetas personales o de IT
```

---

## CONDICIONES CLAVE
- Solo .kdbx → keepass2john sin -k → crackear con john/hashcat
- .kdbx + archivo clave → keepass2john con -k → crackear
- Contraseña encontrada → abrir directamente con keepassxc
- Vault abierto → revisar TODO → puede contener DA, servicios, infraestructura

## ONE-LINERS MENTALES
- Encuentro .kdbx → probar contraseñas conocidas primero → luego crackear
- Crackeo exitoso → abrir con keepassxc → revisar todas las entradas
- Hay archivo .key en el mismo directorio → usarlo con keepass2john -k
- Vault abierto → copiar todas las contraseñas → probar en SMB / WinRM / RDP
