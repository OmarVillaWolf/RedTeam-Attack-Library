# ADScan 

Tags: #AD #Kali 

**ADscan** es una herramienta de línea de comandos para evaluar la seguridad de **Active Directory (AD)**, el sistema que muchas organizaciones usan para administrar usuarios, equipos y permisos en una red Windows.

Automatiza tareas que también se pueden hacer con herramientas separadas: descubrir información del dominio, buscar configuraciones débiles y organizar posibles rutas de acceso. Puede generar datos para analizarlos en BloodHound, aunque **no es lo mismo que BloodHound**: BloodHound se centra en visualizar y analizar relaciones y rutas; ADscan reúne varias tareas en un flujo de trabajo.

* [ADScan](https://github.com/ADScanPro/adscan)

```bash 
❯ adscan start    # Iniciar la herramienta 

# Iniciar el tipo de escaneo 
❯ start_unauth 
❯ start_auth

# Authenticated scan of a domain
❯ adscan ci auth --type audit --interface eth0 \
  --domain corp.local --dc-ip 10.0.0.1 -u alice -p 'S3cr3t!'

# Unauthenticated sweep
❯ adscan ci unauth --type audit --interface eth0 --dc-ip 10.0.0.1
```