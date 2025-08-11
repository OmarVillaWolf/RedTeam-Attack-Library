# Enumeración 

Tags: #Hacking #CloudComputing  

## Lazys3 

```bash 
# Kali Tool para enumerar 'S3 buckets' 
❯ ruby lazys3.rb                   # Ejecutar la tool y muestra una lista de 'buckets'

❯ ruby lazys3.rb HackerOne         # Busca los 'buckets'
```

## S3Scanner 

```bash 
# Kali Tool para enumerar 'S3 buckets' 
❯ cd S3Scanner/                            # Ir al dir que contiene la tool 
	❯ pip3 install -r requirements.txt    # Instalar los requerimientos 

❯ python3 s3scanner.py sites.txt           # Ejecutar la tool para escanear los sitios web con 's3 buckets'
❯ python3 s3scanner.py --include-closed --out-file found.txt --dump names.txt  # Muestra los buckets abiertos en el archivo dado 
	❯ python3 s3scanner.py names.txt      
❯ python3 s3scanner.py --list names.txt    # Guarda en una lista los nombres de los bukets abiertos 
```