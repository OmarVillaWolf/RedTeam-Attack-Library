# Escalación de privilegios en Windows

Tags: #Windows #PrivEsc #Meterpreter

## Identificación de vulnerabilidades 

* [Escalacion-Privilegios-Payloadallthethings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md)
* [PrivescCheck](https://github.com/itm4n/PrivescCheck)

```bash 
❯ powershell -ep bypass -c ". .\PrivescCheck.ps1; Invoke-PrivescCheck"    # Recopila informacion del PrivEsc
```