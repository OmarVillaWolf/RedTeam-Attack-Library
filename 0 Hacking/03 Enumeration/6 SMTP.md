# Enumeración SMTP (Simple Mail Transfer Protocol)

Tags: #Hacking #Enumeration #SMTP 

## Nmap 

```bash 
# Kali Tool 
❯ nmap -p 25 --script smtp-enum-users IP     # Enumera usuarios 
❯ nmap -p 25 --script smtp-commands IP       # Enumera los comandos que se pueden utilizar  
```