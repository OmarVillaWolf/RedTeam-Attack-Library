# UDPZ

Tags: #UDP 

Es un **escáner UDP rápido basado en probes** — la alternativa a `nmap -sU` pero mucho más rápida y precisa.

## Instalación 
```bash 
# Instalación con GO   <-  MEJOR FORMA
❯ go install github.com/FalconOpsLLC/udpz@latest
❯ export PATH=$PATH:~/go/bin
```

```bash 
Forma 2: Instalación 
❯ git clone https://github.com/FalconOpsLLC/udpz && cd udpz && go build .
```

### Comandos 
```bash 
❯ udpz -help   # Mirar el panel de ayuda 

❯ udpz IP      # Escaneo de puertos UDP 
```