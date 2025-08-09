# Ataques de transferencia de zona (AXFR – Full Zone Transfer)

Tags: #TransferZone #OWASP #Explotacion #Amass #Subfinder #Dig #DnsRecon #DnsEnum #Whois #DnsLookup #DC 

Los ataques de transferencia de zona, también conocidos como ataques **AXFR**, son un tipo de ataque que se dirige a los servidores **DNS** (**Domain Name System**) y que permite a los atacantes obtener información sensible sobre los dominios de una organización.

En términos simples, los servidores DNS se encargan de traducir los nombres de dominio legibles por humanos en direcciones IP utilizables por las máquinas. Los ataques AXFR permiten a los atacantes obtener información sobre los registros DNS almacenados en un servidor DNS.

El ataque AXFR se lleva a cabo enviando una solicitud de transferencia de zona desde un servidor DNS falso a un servidor DNS legítimo. Esta solicitud se realiza utilizando el protocolo de transferencia de zona DNS (AXFR), que es utilizado por los servidores DNS para transferir registros DNS de un servidor a otro.

Si el servidor DNS legítimo no está configurado correctamente, puede responder a la solicitud de transferencia de zona y proporcionar al atacante información detallada sobre los registros DNS almacenados en el servidor. Esto incluye información como los nombres de dominio, direcciones IP, servidores de correo electrónico y otra información sensible que puede ser utilizada en futuros ataques.

Una de las herramientas que utilizamos en esta clase para explotar el AXFR es **dig**. El comando dig es una herramienta de línea de comandos que se utiliza para realizar consultas DNS y obtener información sobre los registros DNS de un dominio específico.

La sintaxis para aplicar el AXFR en un servidor DNS es la siguiente:

➜ `dig @<DNS-server> <domain-name> AXFR`

Donde: 

-   **❮DNS-server❯** es la dirección IP del servidor DNS que se desea consultar.
-   **❮domain-name❯** es el nombre de dominio del cual se desea obtener la transferencia de zona.
-   **AXFR** es el tipo de consulta que se desea realizar, que indica al servidor DNS que se desea una transferencia de zona completa.

Para prevenir los ataques AXFR, es importante que los administradores de red configuren adecuadamente los servidores DNS y limiten el acceso a la transferencia de zona solo a servidores autorizados. También es importante mantener actualizado el software del servidor DNS y utilizar técnicas de cifrado y autenticación fuertes para proteger los datos que se transmiten a través de la red. Los administradores también pueden utilizar herramientas de monitoreo de DNS para detectar y prevenir posibles ataques de transferencia de zona.

A continuación, se proporciona el enlace correspondiente al proyecto de Github que nos descargamos para desplegar un entorno en Docker vulnerable con el que poder practicar:

-   **DNS-Zone-Transfer**: [https://github.com/vulhub/vulhub/tree/master/dns/dns-zone-transfer](https://github.com/vulhub/vulhub/tree/master/dns/dns-zone-transfer)

Paginas de ayuda para el DNS Zone Transfer:
* [DNSDumpster](https://dnsdumpster.com/)

Registros DNS 
* A            -       Resuelve un dominio de hostname de un IPV4
* AAAA    -       Resuelve un dominio de hostname  de un IPV6
* NS         -       Referencia a los dominios 'nombre del servidor'
* MX        -       Resuelve un dominio al servidor de mail 
* CNAME -      Usado para los alias de dominio
* TXT       -       Registros de texto 
* HINFO   -      Información del host 
* SOA       -      Autoridad de dominio
* SRV        -      Registros de servicio 
* PTR        -      Resuelve una dirección IP de un hostname 

DNS (Domain Name System) es crucial para el funcionamiento de Active Directory ya que se utiliza para localizar controladores de dominio y otros servicios críticos. Un servidor DC suele ser también un servidor DNS en entornos de AD.
## Whois y DNS Dumping 

```bash 
❯ whois domain.com      # Busca info sobre el dominio 
```

```bash 
❯ nslookup domain.com    # Consulta la configuracion DNS de un dominio y te da su direccion IP

❯ nslookup
	❯ server IP           # Muestra el nombre de dominio 
	❯ IP
```

## Dig

```powershell
1. 'Dig' ayuda a identificar el servidor real detras del dominio 
	❯ dig domain.com    # Obtener la IP del dominio 

2. Con la IP se puede hacer un escaneo de puertos y servicios con 'Nmap'
3. Se puede identificar si la infraestrucrura es compartida. Varios subdominios apunten a la misma IP
4. Ayuda a la evasión por WAF/CDN que estan protegidas por 'Cloudflare' que protegen el dominio pero no la IP directa 
```

```bash
❯ dig @<IP> <Domain> 
❯ dig mx @<IP> <Domain>         # Enumerar los servidores de correo
❯ dig ns @<IP> <Domain>         # Enumerar los name-servers
```

```bash
❯ dig axfr @<IP> <Domain>       # Ejecutar el ataque de transferencia de Zona para enumerar todos los subdominios existentes 

	# Dominio = Debemos de conocer el dominio anteriormente, ya que si no no funcionaria
```

## DNS Records

```bash  
❯ dnsrecon -d <domain.com>     # Para ver los registros (Servidores) 
```

```bash 
❯ dnsenum zonetransfer.me      # Reconocimiento de registros (Servidores)
```

```bash 
❯ fierce -dns <domain.com>     # Reconocimiento 
```

```bash 
❯ ldns-walk @<IP> <Domain>     # Obtener info de la red en un determinado dominio como sus registros 
```

## Amass 

```bash 
❯ https://github.com/owasp-amass/amass      # Descargar la tool 
```

```bash 
❯ amass enum -d domain.com                  # # Enumerar subdominios para mirar si existen aplicaciones distintas, tecnologias diferentes, vulnerabilidades propias y credenciales separadas
```

## Subfinder 

```bash 
# Enumerar subdominios para mirar si existen aplicaciones distintas, tecnologias diferentes, vulnerabilidades propias y credenciales separadas
❯ https://github.com/projectdiscovery/subfinder   
```

```bash 
❯ subfinder -h      # Mirar la ayuda 
```