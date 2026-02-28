# Trusts 

Tags: #AD #Powershell #PowerView #PowershellModule 

```bash 
En un entorno de Active Directory (AD), la 'relación de confianza' ('trust') es una relación entre dos dominios o bosques que permite a los usuarios de un dominio o bosque 'acceder a recursos' en el otro dominio o bosque.  
- La confianza puede ser 'automática' (como entre dominios padre e hijo dentro del mismo bosque) o 'establecida manualmente' (como entre bosques o dominios externos).  
- Los 'Objetos de Dominio de Confianza (TDOs)' representan las relaciones de confianza dentro de un dominio.
```

```bash 
Direcciones:
1. 'Confianza unidireccional (One-way trust)' – Es unidireccional. Los usuarios del dominio de confianza (trusted domain) pueden acceder a los recursos del dominio que confía (trusting domain), pero no ocurre lo contrario. 
   -->   =   La diección de la flecha significa que dominio confia en quien. No significa quien se puede conectar con quien.
   Domain A --> Domain B   = El dominio A confía en el dominio B, por lo tanto los usuarios del dominio B se pueden conectar a los servidores del dominio A. 

2. 'Confianza bidireccional (Two-way trust)' – Es bidireccional. Los usuarios de ambos dominios pueden acceder a los recursos del otro dominio.
```

```bash 
1. Transitividad:
- Puede extenderse para establecer relaciones de confianza con otros dominios. Todas las relaciones de confianza intra-bosque predeterminadas (raíz del árbol, padre-hijo) entre dominios dentro de un mismo bosque son 'confianzas transitivas bidireccionales'.

2. No transitiva:
- No puede extenderse a otros dominios dentro del bosque. Puede ser bidireccional o unidireccional. Esta es la confianza predeterminada (llamada confianza externa) entre dos dominios en bosques diferentes cuando los bosques no tienen una relación de confianza.
```

```bash 
Confianzas predeterminadas/automáticas

1. Confianza padre-hijo  
- Se crea automáticamente entre el nuevo dominio y el dominio que lo precede en la jerarquía del espacio de nombres, cada vez que se añade un nuevo dominio en un árbol. Por ejemplo, dollarcorp.moneycorp.local es un dominio hijo de moneycorp.local.  
- Esta confianza es siempre 'bidireccional y transitiva'.

2. Confianza raíz de árbol  
- Se crea automáticamente cada vez que se añade un nuevo árbol de dominios a la raíz del bosque.  
- Esta confianza es siempre 'bidireccional y transitiva'.

3. Confianzas externas 
- Entre dos dominios en bosques diferentes cuando los bosques no tienen una relación de confianza.  
- Puede ser unidireccional o bidireccional y 'no es transitiva'.

4. Confianzas entre bosques (Forest Trusts)  
- Entre los dominios raíz de los bosques.  
- No pueden extenderse a un tercer bosque (no hay confianza implícita).  
- Pueden ser unidireccionales o bidireccionales y son transitivas.
```

## PowerView 

* [PowerView](https://github.com/ZeroDayLab/PowerSploit/blob/master/Recon/PowerView.ps1)

```powershell 
❯ . C:\AD\PowerView.ps1               # Cargar PowerView en memoria 
❯ Import-Module .\PowerView.ps1       # Importar el módulo 
```

```powershell 
❯ Get-DomainTrust      # Obtener una lista de los 'trust' para el dominio actual 
	- El comando muestra los diferentes 'Trust' 
	- TrustAttributes = WITHIN_FOREST    # Dentro del mismo forest 
	- TrustAttributes = FILTER_SIDS      # Es un 'trust' externo entre dos forest  
	- TrustDirection = Bidirectional     # Ambas direcciones 

❯ Get-DomainTrust | ?{$_.TrustAttributes -eq "FILTER_SIDS"}   # Obtener los external trust

❯ Get-DomainTrust -Domain us.domain1.local

❯ Get-ForestDomain     # Obtener todos los dominios en el bosque actual
❯ Get-ForestDomain -Forest domain2.local  # Obtener los dominios en un forest en específico 

# Mostrar todos los trusts existentes en un forest en específico
❯ Get-ForestDomain -Forest domain2.local | %{Get-DomainTrust -Domain $_.Name} 

❯ Get-Forest           # Obtener detalles sobre el bosque actual 
❯ Get-Forest -Forest domain2.local

❯ Get-ForestGlobalCatalog    # Obtener todos los catálogos globales del bosque actual
❯ Get-ForestGlobalCatalog -Forest domain2.local

# Mapear (o visualizar) las relaciones de confianza de un bosque (sin relaciones de confianza entre bosques en el laboratorio)
❯ Get-ForestTrust      
❯ Get-ForestTrust -Forest domain2.local
```

## Powershell Module 

* [PowerShell AD Module](https://learn.microsoft.com/en-us/powershell/module/activedirectory/?view=windowsserver2025-ps)
* [ADModule](https://github.com/samratashok/ADModule)

```powershell 
❯ Import-Module C:\AD\Tools\Microsoft.ActiveDirectory.Management.dll    # Importar el módulo
❯ Import-Module C:\AD\Tools\ActiveDirectory.psd1    # Importar el módulo
```

```powershell 
❯ Get-ADTrust     # Obtener una lista de las relaciones de confianza de dominio para el dominio actual
❯ Get-ADTrust -Identity us.dollarcorp.moneycorp.local

❯ Get-ADForest    # Obtener detalles sobre el bosque actual 
❯ Get-ADForest -Identity eurocorp.local

❯ (Get-ADForest).Domains    # Obtener todos los dominios en el bosque actual

❯ Get-ADForest | select -ExpandProperty GlobalCatalogs    # Obtener todos los catálogos globales del bosque actual

❯ Get-ADTrust -Filter 'msDS-TrustForestTrustInfo -ne "$null"'   # Mapear (o visualizar) las relaciones de confianza de un bosque (sin relaciones de confianza entre bosques en el laboratorio)
```