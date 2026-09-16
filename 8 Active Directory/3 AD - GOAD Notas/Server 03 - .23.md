# Enumeración inicial en host Windows unido a dominio (usuario interno estándar)

Tags: #AD 

## Enumeración inicial fuera del server 

```powershell
❯ nxc smb 192.168.56.23 -u 'a' -p '' --shares  

# Resultado del smb 
SMB         192.168.56.23   445    BRAVO          CertEnroll                      Active Directory Certificate Services share


Nota: 
	- Cuando hay un CertEnroll visible significa que hay un ADCS (Active Directory Cetificate Services) donde se puede aprovechar para un ESC 
```


---

# Enumeración dentro del server 
