# PowerShell — Referencia de Comandos

Tags: #Windows #PowerShell #Enumeracion 

Referencia rápida de comandos PowerShell organizados por contexto de uso. Complementa la nota de Windows_Comandos.md con comandos específicos de PowerShell.

* [Cheatsheet](https://gist.github.com/HarmJ0y/184f9822b195c52dd50c379ed3117993)

```powershell 
# Enumerar usuarios del dominio 
❯ Get-NetUser | select cn

# Enumerar los grupos del dominio que tienen la palabra 'admin'
❯ Get-NetGroup -GroupName *admin*

# Enumerar los shares 
❯ Get-NetShare
❯ Get-NetShare -ComputerName <PC_Name>

# Identificar el sistema operativo 
❯ Get-NetComputer -FullData | Select-Object dnshostname, operatingsystem
```