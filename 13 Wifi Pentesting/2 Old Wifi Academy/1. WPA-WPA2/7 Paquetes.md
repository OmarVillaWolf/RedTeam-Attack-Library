# Análisis de paquetes con filtros 

Tags: #Paquetes #Probes #Association #Beacon #Autenticacion #Deautenticacion #Desasociacion #Attack #WPA/WPA2 

##  Probe Request 

```bash 
1. El 'probe request' son paquetes que emite el movil ya que quiere conectarse al AP.
2. El 'probe responde' son paquetes que el AP emite cuando responde a las peticiones del movil para generar la conexión

❯ tshark -r captura.cap -Y "wlan.fc.type_subtype==4" 2>/dev/null           # Filtramos por los paquetes probe request 
```

## Probe Response 

```bash
❯ tshark -r captura.cap -Y "wlan.fc.type_subtype==5" 2>/dev/null           # Filtramos por los paquetes probe response 
```

## Association Request 

```bash 
❯ tshark -r captura.cap -Y "wlan.fc.type_subtype==0" -Tfields -e wlan.addr 2>/dev/null | tr ',' '\n' | sort -u 
# Filtramos por los paquetes association request en donde podemos observas las direcciones MAC de los clientes 
	# tr = Las comas las convertimos a saltos de linea
	# sort = Listamos cadenas unicas 
```

## Association Response 

```bash 
❯ tshark -r captura.cap -Y "wlan.fc.type_subtype==1" 2>/dev/null           # Filtramos por los paquetes association response 
```

## Beacon 

```bash 
❯ tshark -r captura.cap -Y "wlan.fc.type_subtype==8" 2>/dev/null               # Filtramos por los paquetes beacon  
❯ tshark -r captura.cap -Y "wlan.fc.type_subtype==8" -Tjson 2>/dev/null        # Observamos los atriubutos de los paquetes beacon
```

## Autenticación  

```bash 
❯ tshark -r captura.cap -Y "wlan.fc.type_subtype==11" 2>/dev/null              # Filtramos por los paquetes de autenticacion 
```

## Deautenticación  

```bash 
❯ tshark -r captura.cap -Y "wlan.fc.type_subtype==12" -Tfields -e wlan.da 2>/dev/null | sort -u 
# Filtramos por los paquetes de deautenticacion de un ataque global, donde obervamos las etsaciones de clientes y la broadcast MAC address
```

## Desasociación  

```bash 
❯ tshark -r captura.cap -Y "wlan.fc.type_subtype==10" 2>/dev/null              # Filtramos por los paquetes de desasociacion
```