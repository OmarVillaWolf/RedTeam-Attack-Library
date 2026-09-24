# SSH (22)  
  
Tags: #SSH #Linux #Auth #Keys #Bruteforce #Pivoting #Tunneling #Persistence  
  
## OBJETIVO  
- Acceder al servicio SSH  
- Validar credenciales  
- Abusar autenticación por llaves  
- Enumerar configuraciones débiles  
- Ejecutar comandos remotos  
- Pivotear a otras redes  

## TIPS  
1. **Si tienes creds → prueba TODO (SSH, su, sudo, pivoting)**    
2. **Si tienes id_rsa → SIEMPRE probar acceso**    
3. **Si SSH permite password → evaluar fuerza bruta / spraying**    
4. **Si entras → revisar authorized_keys (persistencia)**    

## TOOLS  
* ssh  
* sshpass  
* nmap  
* ssh-audit  
* proxychains  
  
## 1. RECONOCIMIENTO / ENUMERACIÓN INICIAL  
  
```bash  
❯ nmap -p22 -sV -sC <IP>  
# Detecta versión SSH + scripts básicos  
  
❯ nmap -p22 --script ssh2-enum-algos <IP>  
# Enum algoritmos soportados (ciphers débiles)  
  
❯ nmap -p22 --script ssh-auth-methods <IP>  
# Métodos de autenticación permitidos (password, publickey)  
  
❯ nmap -p22 --script ssh-hostkey <IP>  
# Obtiene fingerprint de la llave del servidor

❯ ssh-audit <IP>  
# Auditoría completa de configuración SSH  
# Detecta:  
# - algoritmos débiles  
# - versiones vulnerables  
# - malas configuraciones
```

## 2. CONEXIÓN BÁSICA

```bash 
❯ ssh user@<IP>  
# Conexión estándar (password o key)

❯ ssh -o HostKeyAlgorithms=ssh-rsa -o PubkeyAcceptedAlgorithms=ssh-rsa user@<IP>
# Si es un server antiguo usar este comando para conectarse por SSH 

❯ ssh user@<IP> -p 2222  
# Puerto no estándar

❯ sshpass -p 'PASSWORD' ssh user@<IP>  
# Login no interactivo (útil en scripts)  
# Password en claro → bajo OPSEC
```

```bash 
❯ ssh-keygen -y -f id_rsa    # Verificar si laa llave privada tiene un 'passphrase'

NOTA:
	- Si la llave privada tiene un passphrase se debe de crackear con Hashcat o John  
```

## 3. AUTENTICACIÓN CON LLAVES

```bash 
❯ ssh -i id_rsa user@<IP>  
# Autenticación con clave privada  
# Requiere:  
# - permisos 600 en id_rsa

❯ chmod 600 id_rsa  
# Ajustar permisos si falla autenticación

❯ ssh user@localhost 
# Login sin password si authorized_keys está configurado
```


## 4. GESTIÓN DE LLAVES (IMPORTANTE)

```bash 
❯ ssh-keygen  
# Genera:  
# - id_rsa (privada)     -> La utilizas para ingresar al server
# - id_rsa.pub (pública) -> Debes agregarla en el 'authorized_keys' del server

❯ cat ~/.ssh/id_rsa.pub | tr -d '\n' | xclip -sel clip  
# Copiar clave pública sin saltos de línea

❯ nvim authorized_keys  
# Ruta típica:  
# /home/user/.ssh/authorized_keys  
# /root/.ssh/authorized_keys
```

### Condiciones clave
- Si puedes escribir en `authorized_keys` → acceso persistente
- Si encuentras `id_rsa` → posible acceso inmediato

## 5. ENUMERACIÓN POST-ACCESO

```bash 
❯ whoami  
❯ id  
❯ sudo -l  
# Ver privilegios

❯ cat ~/.ssh/authorized_keys  
❯ ls -la ~/.ssh/  
# Revisar llaves existentes

❯ history  
# Buscar credenciales
```

## 6. MOVIMIENTO LATERAL

```bash 
❯ ssh user@<IP2>  
# Reutilización de credenciales

❯ ssh -i id_rsa user@<IP2>  
# Reutilización de llaves
```


## 7. PERSISTENCIA

```bash 
❯ echo "ssh-rsa AAAAB3..." >> ~/.ssh/authorized_keys  
# Backdoor SSH
```

### Requisitos
- Permiso de escritura en ~/.ssh/

## 8. CASOS ESPECIALES

```bash 
❯ ssh -o StrictHostKeyChecking=no user@<IP>  
# Evita prompt de fingerprint

❯ ssh -o PreferredAuthentications=password user@<IP>  
# Forzar password auth

❯ ssh -o PubkeyAuthentication=no user@<IP>  
# Desactivar uso de llaves
```
