# Bash

Tags: #Comandos #Bashshell 

```bash 
❯ bash script.sh            # Ejecutar un script sin permisos de ejecución
❯ ./script.sh               # Ejecutar un script con permisos de ejecución 
```

## Obtener la IP 

```bash 
#!/bin/bash 

# Agregar Colores
greenColour="\e[0;32m\033[1m"
endColour="\033[0m\e[0m"
redColour="\e[0;31m\033[1m"
blueColour="\e[0;34m\033[1m"
yellowColour="\e[0;33m\033[1m"
purpleColour="\e[0;35m\033[1m"
turquoiseColour="\e[0;36m\033[1m"
grayColour="\e[0;37m\033[1m"

echo -e "\n${yellowColour}[+]${endColour} Esta es tu direccion IP privada -> $(ip a | grep ens33 | tail -n 1 | awk '{print $1}' FS="/")\n"
	# e = Interpretar caracteres especiales (\n = salto de linea), colores.
	# Cada color que se quiera agregar, se debe de abrir y cerrar 
```

## Descubriendo interfaces con ping 

```bash 
#!/bin/bash 

function ctrl_c(){
	echo -e "\n\n[!] Saliendo...\n"
	tput cnorm; exit 1
}

# Ctrl_c
trap ctrl_c INT

tput civis

for i in $(seq 1 254); do
	timeout 1 bash -c "ping -c 1 10.10.0.$i" &>/dev/null && echo "[+] Host 10.10.0.$i - ACTIVE" &
done; wait

tput cnorm
```

## Descubriendo interfaces con puertos mas comunes 

```bash
!#/bin/bash 

function ctrl_c(){
	echo -e "\n\n[!] Saliendo...\n"
	tput cnorm; exit 1
}

# Ctrl_c
trap ctrl_c INT

tput civis

for i in $(seq 1 254); do
	for port in 21 22 80 443 445 8080; do
		timeout 1 bash -c "echo '' > /dev/tcp/10.10.0.$i/$port" &>/dev/null && echo "[+] Host 10.10.0.$i - PORT $port - OPEN" &
	done 
done; wait 

tput cnorm
```

```bash 
#!/bin/bash 

function ctrl_c(){
	echo -e "\n\n[!] Saliendo...\n"
	tput cnorm; exit 1
}

# Ctrl_c
trap ctrl_c INT

tput civis

for port in $(seq 1 65535); do
	timeout 1 bash -c "echo '' > /dev/tcp/172.19.0.1/$port" 2>/dev/null && echo "[+] Puerto $port abierto" & 
done; wait

tput cnorm
```
