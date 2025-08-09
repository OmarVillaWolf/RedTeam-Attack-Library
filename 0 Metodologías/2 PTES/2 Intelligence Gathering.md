# Intelligence Gathering 

Tags: #PTES 

```bash 
1. Objetivo: Obtener toda la información posible del objetivo (públicamente o pasivamente).

2. Qué haces?
    - Recolección pasiva: WHOIS, DNS, direcciones IP, correos, metadatos, etc.
    - OSINT para conocer tecnología, empleados, estructura.
```

## Reconocimiento Pasivo 

```bash 
1. WEB
Herramientas útiles:
    - 'theHarvester' → Emails, hosts, subdominios.
    - 'Shodan' → Puertos y servicios expuestos públicamente.
    - 'Amass / Sublist3r' → Enumeración de subdominios.
    - 'Maltego' → Visualización de relaciones.
    - 'FOCA / EXIFTool' → Extracción de metadatos en documentos.
    - 'BurpSuite' Crawling 
    - 'WayBackMachine' → Mirar las versiones anteriores de la Web 
    - 'Crt.sh' → Visualizar info de certificados y subdominios 

2. Infra
Herramientas útiles:
    - 'Wireshark'
    - 'Responder'
    - 'Shodan / Censys'
    - 'TcpDump'
```

## Reconocimiento Activo 

```bash 
1. WEB
Herramientas útiles:
    - 'Nikto' 
    - 'Nmap'
    - 'Gobuster / Dirbuster'

2. Infra
Herramientas útiles:
    - 'Enum4linux'
    - 'Nextec'
    - 'Rpcclient'
    - 'BloodHound'
    - 'Impacket-tools'
    - 'SmbMap'
    - 'SmbClient'
```