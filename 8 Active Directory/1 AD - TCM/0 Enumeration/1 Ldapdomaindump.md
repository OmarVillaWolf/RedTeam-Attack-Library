# Ldapdomaindump 

Tags: #AD #LDAP 


```bash 
1. Crear un folder ya que la herramienta nos crea varios archivos 
❯ mkdir Domain

2. Ejecutamos el comando
❯ ldapdomaindump ldaps://IP -u 'Domain\User' -p Password    # Crea archivos donde podemos ver el 'LDAP Domain' que incluye: domain_computers, domain_groups, domain_policy, domain_users_by_group, etc...
```

