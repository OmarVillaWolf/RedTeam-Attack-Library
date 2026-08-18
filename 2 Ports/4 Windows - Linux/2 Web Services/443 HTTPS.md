# Enumeración de servicio HTTPS

Tags: #Web #Reconocimiento #Escaneo  #HTTPS 


```bash
❯ openssl s_client -connect domain.com:443   # Conectarse a openssl e inspeccionar el certificado

❯ sslyze domain.com       # Inspeccionar el certificado SSL
```

```bash
❯ sslscan domain.com:8443      # Mostrar información del ssl de la máquina y si detecta alguna vulnerabilidad te la representa. Si hay un puerto especifico colocarlo 
```