## Wireshark

Tags: #Hacking #NetworkScanning #OSDiscovery 

| Operating System | Time To Live | TCP Windows Size          |
| ---------------- | ------------ | ------------------------- |
| Linux            | 64           | 5840                      |
| FreeBSD          | 64           | 65535                     |
| OpenBSD          | 255          | 16384                     |
| Windows          | 128          | 65535 bytes to 1 Gigabyte |
| Cisco Routers    | 255          | 4128                      |
| Solaris          | 255          | 8760                      |
| AIX              | 255          | 16384                     |

```bash 
# Windows tool 
❯ wireshark.exe

Nota: 
	1. Generar trafico 'ICMP' desde el cmd con el comando ping para verlo en 'Wireshark'
	2. Mirar en el 'Reply' el TTL para saber que Sistema Operativo es
```

## Nmap 

```bash 
# Kali tool 
❯ nmap -A IP          # Escaneo agresivo completo 
❯ nmap -O IP          # Muestra el OS 
❯ nmap --script smb-os-discovery IP   # Muestra mas detalles del smb 
```

## Unicornscan

```bash 
# Kali tool 
❯ unicornscan IP -Iv     # Muestra puertos abiertos, TTL

	# I = Especifica el modo 
	# v = Verbosidad 
```