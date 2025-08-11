# DNS

Tags: #PassiveRecon 

* [Registros-DNS](https://www.cloudflare.com/es-es/learning/dns/dns-records/)

```bash 
❯ https://dnsdumpster.com/          # Muestra los registros del dominio 
❯ https://www.nslookup.io/
```

## Kali tools 

```bash 
❯ dnsrecon -r 1.1.1.1-2.2.2.2       # Busqueda reversa de IP la cual muestra los nombres de dominio
❯ dig A domain.com                  # Buscamos registros de tipo 'A'
```