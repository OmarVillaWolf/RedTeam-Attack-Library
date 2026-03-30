# Active Directory 

Tags: #AD 

Estructura:
- Bosques, dominios y unidades organizacionales (OUs) son los bloques básicos de cualquier estructura de AD 
- Un bosque el cual es un límite, puede contener múltiples dominios y cada dominio contener múltiples OUs.

## Ejemplo en PowerView

```bash 
Forest                   :  moneycorp.local                         # Bosque 
DomainControllers        :  {dcorp-dc.dollarcorp.moneycorp.local}   # Controlador de dominio DC
Children                 :  {us.dollarcorp.moneycorp.local}         # Dominio hijo
DomainMode               :  Unknow 
DomainModeLevel          :  7                                       
Parent                   :  moneycorp.local
PdcRoleOwner             :  dcorp-dc.dollarcorp.moneycorp.local
RidRoleOwner             :  dcorp-dc.dollarcorp.moneycorp.local
InfrastructureRoleOwner  :  dcorp-dc.dollarcorp.moneycorp.local
Name                     :  dollarcorp.moneycorp.local               # Nombre del dominio
```

## Analogía simple

```bash 
Forest = empresa
Dominio = departamento
Trust = convenio entre empresas
```
