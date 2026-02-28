# RustScan 

Tags: #RustScan #Escaneo 


```bash 
❯ https://github.com/bee-san/RustScan         # Descargar la tool de releases 

❯ sudo dpkg -i rustscan_2.4.1-1_amd64.deb     # Instalar la tool 
```

## Comandos

```bash 
❯ rustscan --version          # Mirar la versión 

❯ rustscan -a ❮IP❯
❯ rustscan -a ❮IP❯ -- -sT     # Mirar puertos abiertos 


❯ rustscan -a ❮IP❯ -- -sCV -oN targeted 
❯ rustscan -a ❮IP❯ -u 5000 -- -sCV -oN targeted    # Controlar la velocidad        

❯ rustscan -a ❮IP❯ -p 22,80,443 -- -sC -sV         # Escanear puertos específicos 
```