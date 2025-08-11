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

```powershell 
❯ Get-DomainTrust      # Obtener una lista de las relaciones de confianza de dominio para el dominio actual 
❯ Get-DomainTrust -Domain us.dollarcorp.moneycorp.local

❯ Get-Forest           # Obtener detalles sobre el bosque actual 
❯ Get-Forest -Forest eurocorp.local

❯ Get-ForestDomain     # Obtener todos los dominios en el bosque actual
❯ Get-ForestDomain -Forest eurocorp.local

❯ Get-ForestGlobalCatalog    # Obtener todos los catálogos globales del bosque actual
❯ Get-ForestGlobalCatalog -Forest eurocorp.local

# Mapear (o visualizar) las relaciones de confianza de un bosque (sin relaciones de confianza entre bosques en el laboratorio)
❯ Get-ForestTrust      
❯ Get-ForestTrust -Forest eurocorp.local
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