# Enumeración local de Windows 

Tags: #LocalEnumeration #PrivEsc #Windows #Meterpreter

## Enumeración de la información de la red local 

### Que estamos buscando?

1. Actual IP Address y adaptador de red 
2. Redes internas 
3. Servicios TCP/UDP que estan corriendo y sus respectivos puertos 
4. Otros host en la red
5. Tablas de ruteo
6. Estado del Firewall de Windows 

## Comandos Windows 

```bash 
❯ ipconfig                              # Muestra la dirección IP y los adaptadores de red 
❯ ipconfig /all                         # Muestra mas detalles de los adaptadores, MAC, DHCP

❯ route print                           # Muestra la tabla de ruteo IPV4 e IPV6

❯ arp -a                                # Muestra la tabla ARP, que es una lista de todos los dispositivos de la red 

❯ netstat -ano                          # Muestra la lista de servicios (TCP/UDP) que estan actualmente corriendo o escuchando 

❯ netsh firewall show state             # Si no funciona, utilizamos el de abajo  
❯ netsh advfirewall firewall dump       
❯ netsh advfirewall show allprofiles    # Muestra el estado del Firewall 
```
