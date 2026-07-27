# Enumeración local de Linux 

Tags: #LocalEnumeration #PrivEsc #Linux #Meterpreter

## Enumeración de la información de la red local 

```bash 
# Que estamos buscando? 

1. Ip actual y su adaptador de red 
2. Redes internas 
3. Servicios que corren TCP/UDP y sus respectivos puertos 
4. Otros hosts en la red
```

## Comandos Linux 

```bash 
❯ ifconfig                  # Muestra las interfaces y su IP
❯ ip a s                    # Muestra las interfaces y los adaptadores 
 
❯ cat /etc/networks         # Lista las interfaces y su configuracion 
❯ cat /etc/hostname         # Muestra el nombre del host 
❯ cat /etc/hosts            # Muestra todos los host y su dominio pertinente 
❯ cat /ect/resolv.conf      # Muestra el nombre y su IP del servidor DNS 

❯ arp -a                    
```

## Con Meterpreter

```bash 
❯ ifconfig         # Muestra las interfaces y su IP
❯ netstat          # Muestra la lista puertos, estado, direcciones IP, direcciones remotas, usuario, PID
❯ route            # Muestra la tabla de ruteo 
❯ arp              # Muestra el ARP cache 
```