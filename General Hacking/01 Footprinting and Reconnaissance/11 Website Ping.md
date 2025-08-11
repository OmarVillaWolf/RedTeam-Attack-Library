# Website Footprinting 

Tags: #Hacking #Footprinting #Website

```bash 
# Windows tool 
❯ ping domain.com -f -l 1500    # Verificar el tamaño correcto aceptado por el server sin ser fragmentado

	# f = No fragmentar los paquetes 
	# l = Tamaño del buffer a enviar en bytes (Variar desde 1500 hacia abajo)]

❯ ping domain.com -i 3          # Verificar el TTL (En Windows es 128, en Linus 64)

	# i = Es el TTL (El número máximo a enviar es de 255 y son la cantidad de saltos)

❯ ping domain.com -i 2 -n 1 

	# n = Numero de paquetes a enviar 
```