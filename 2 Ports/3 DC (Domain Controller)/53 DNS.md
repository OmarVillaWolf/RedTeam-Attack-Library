# DNS (53)

Tags: #DNS #AXFR #ZoneTransfer #Reconocimiento #DC #AD

## OBJETIVO
- Identificar el dominio y servidor DNS
- Enumerar registros DNS (A, MX, NS, SRV, PTR)
- Intentar transferencia de zona (AXFR)
- Descubrir subdominios y servicios ocultos
- En entornos AD → localizar el DC y servicios críticos

## TIPS
1. **En AD → el DC casi siempre es también el servidor DNS**
2. **AXFR sin autenticación → pruébalo siempre, muchos servidores mal configurados lo permiten**
3. **Registros SRV → revelan servicios críticos como LDAP y Kerberos con sus IPs y puertos**
4. **Si encuentras subdominios → cada uno puede tener su propia superficie de ataque**
5. **dig vs nslookup → dig da más detalle técnico, nslookup es más rápido para consultas simples**

## TOOLS
* [DNSDumpster](https://dnsdumpster.com/)
* [Subfinder](https://github.com/projectdiscovery/subfinder)
* [DNSRecon](https://github.com/darkoperator/dnsrecon)

---

## REFERENCIA DE REGISTROS DNS

| Registro | Descripción |
|---|---|
| A | Resuelve hostname a IPv4 |
| AAAA | Resuelve hostname a IPv6 |
| NS | Name servers del dominio |
| MX | Servidores de correo |
| CNAME | Alias de dominio |
| TXT | Registros de texto (SPF, DKIM, info) |
| SOA | Autoridad del dominio |
| SRV | Servicios con puerto y host (clave en AD) |
| PTR | Resolución inversa IP → hostname |
| HINFO | Información del host |

---

## 1. RECONOCIMIENTO INICIAL

```bash
❯ nslookup <domain.com>
# No requiere creds → resolución básica del dominio
# Devuelve IP del servidor DNS y registros A

❯ nslookup <IP>
# Resolución inversa → obtener hostname desde IP
# Útil para confirmar si una IP es un DC

❯ nslookup -type=srv _ldap._tcp.dc._msdcs.<domain.corp> <IP>
# No requiere creds → confirmar IP y puerto del DC en entornos AD
# Si devuelve resultado → confirma que es un DC

❯ whois <domain.com>
# No requiere creds → info del registrador, organización, fechas
# Útil en engagements externos para reconocimiento inicial
```

### Insight
- El resultado de nslookup → agregar al /etc/hosts inmediatamente
- Si ves `_ldap._tcp.dc._msdcs` → ya confirmaste que hay un DC

---

## 2. ENUMERACIÓN DE REGISTROS

```bash
❯ dig <domain.com>
# No requiere creds → registro A del dominio (IP principal)

❯ dig @<IP> <domain.com>
# No requiere creds → consultar un servidor DNS específico directamente

❯ dig mx @<IP> <domain.com>
# No requiere creds → servidores de correo del dominio
# Útil para identificar tecnología de mail (Exchange, G Suite, etc.)

❯ dig ns @<IP> <domain.com>
# No requiere creds → name servers del dominio
# Revela todos los servidores DNS autoritativos

❯ dig txt @<IP> <domain.com>
# No requiere creds → registros TXT
# Puede contener info de SPF, DKIM, verificaciones de dominio

❯ dig any @<IP> <domain.com>
# No requiere creds → todos los registros disponibles de una vez
# No siempre funciona → algunos servidores lo bloquean
```

---

## 3. REGISTROS SRV (CLAVE EN AD)

```bash
❯ dig -t srv _ldap._tcp.<domain.corp> @<IP>
# No requiere creds → localizar servidor LDAP (puerto 389) del dominio
# En AD → apunta directamente al DC

❯ dig -t srv _kerberos._tcp.<domain.corp> @<IP>
# No requiere creds → localizar servidor Kerberos (puerto 88)
# Confirma ubicación del DC

❯ dig -t srv _kerberos._tcp.dc._msdcs.<domain.corp> @<IP>
# No requiere creds → localizar DC específicamente
# Más preciso que el anterior en entornos multi-DC

❯ nslookup -type=srv _ldap._tcp.dc._msdcs.<domain.corp> <IP>
# Alternativa con nslookup → mismo resultado
```

### Insight
- Registros SRV en AD → mapa completo de dónde están los servicios críticos
- Si LDAP apunta a una IP diferente al DC principal → puede haber otro DC

---

## 4. TRANSFERENCIA DE ZONA (AXFR)

```bash
❯ dig axfr @<IP> <domain.com>
# No requiere creds → solicitar transferencia de zona completa
# Si el servidor está mal configurado → devuelve TODOS los registros DNS
# Incluye: subdominios, IPs internas, servidores ocultos

❯ dig axfr @<IP> <subdomain.domain.com>
# No requiere creds → AXFR de una zona específica
# Prueba con cada subdominio encontrado

❯ dnsrecon -d <domain.com> -t axfr
# No requiere creds → intenta AXFR automáticamente contra todos los NS
# Más cómodo que hacer dig manual por cada NS

❯ dnsenum <domain.com>
# No requiere creds → reconocimiento completo + intenta AXFR
# Combina enum de registros y zone transfer en un solo comando
```

### Condiciones clave
- AXFR exitoso → lista completa de subdominios e IPs internas → alto valor
- AXFR fallido → `Transfer failed` o `REFUSED` → servidor bien configurado
- Prueba contra TODOS los name servers → a veces el secundario está mal configurado

---

## 5. ENUMERACIÓN DE SUBDOMINIOS

```bash
❯ subfinder -d <domain.com>
# No requiere creds → descubre subdominios vía fuentes pasivas
# Rápido y sin interactuar con el servidor objetivo

❯ subfinder -d <domain.com> -o subdomains.txt
# Guardar resultados a archivo para uso posterior

❯ dnsrecon -d <domain.com>
# No requiere creds → enum de registros + intento de subdominios
# Combina múltiples técnicas en un solo comando

❯ fierce --domain <domain.com>
# No requiere creds → fuerza bruta de subdominios con wordlist interna
# Más agresivo que subfinder → genera más ruido

❯ dnsenum <domain.com>
# No requiere creds → enum completa + brute force de subdominios
# Combina AXFR + enum de registros + subdominios
```

### Insight
- Cada subdominio nuevo → nueva superficie de ataque
- Subdominios internos revelados por AXFR → mapear en /etc/hosts y escanear
- dev. / staging. / internal. → suelen tener controles de seguridad más laxos

---

## 6. RESOLUCIÓN INVERSA

```bash
❯ dig -x <IP> @<servidor_DNS>
# No requiere creds → resolver IP a hostname
# Útil para descubrir hostnames de IPs encontradas en otros escaneos

❯ dnsrecon -r <rango_IP/CIDR> -n <servidor_DNS>
# No requiere creds → resolución inversa de un rango completo
# Descubre todos los hostnames de una subred
# Ejemplo: dnsrecon -r 10.10.10.0/24 -n 10.10.10.1
```

### Insight
- Resolución inversa de rangos → descubrir hosts que no aparecen en otros escaneos
- En entornos AD → puede revelar servidores internos con nombres descriptivos

---

## CONDICIONES CLAVE
- Sin creds → todas las técnicas aplican
- AXFR exitoso → máxima información de la red interna
- Registros SRV → localizar servicios AD sin necesidad de escanear
- Subdominios nuevos → escanear puertos y buscar aplicaciones web

## ONE-LINERS MENTALES
- Puerto 53 abierto → intentar AXFR siempre
- AXFR exitoso → agregar todos los subdominios e IPs al /etc/hosts
- Registros SRV → confirman ubicación de DC, LDAP y Kerberos
- Subdominio nuevo → escaneo de puertos completo + buscar apps web
- En AD → DNS apunta al DC → agregar al /etc/hosts antes de todo
