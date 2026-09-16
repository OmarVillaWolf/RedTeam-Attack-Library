# CRTP - Attack Paths Lab

## Estructura del Entorno

| Dominio | Forest | Rol |
|---|---|---|
| `dollarcorp.moneycorp.local` | moneycorp.local | Dominio principal (donde estás) |
| `moneycorp.local` | moneycorp.local | Dominio padre (Forest Root) |
| `us.dollarcorp.moneycorp.local` | moneycorp.local | Dominio hijo |
| `eurocorp.local` | eurocorp.local | Forest externo (trust con dcorp) |
| `eu.eurocorp.local` | eurocorp.local | Dominio hijo de eurocorp |

### Servidores por Dominio

| Servidor | Dominio | Notas |
|---|---|---|
| Student VM | dollarcorp | Tu punto de entrada |
| dcorp-ci | dollarcorp | Tiene Jenkins |
| dcorp-mgmt | dollarcorp | cladmin es local admin |
| dcorp-mssql | dollarcorp | Tiene Database Links hacia eurocorp |
| dcorp-adminsrv | dollarcorp | Tiene constrained delegation |
| dcorp-appsrv | dollarcorp | appadmin es local admin |
| dcorp-sql1 | dollarcorp | Tiene unconstrained delegation |
| dcorp-dc | dollarcorp | Domain Controller |
| us-dc | us.dollarcorp | Domain Controller del dominio hijo |
| mcorp-dc | moneycorp | Forest Root DC |
| eurocorp-dc | eurocorp | Forest externo DC |
| eu-sql | eu.eurocorp | SQL Server del dominio hijo de eurocorp |
| eu-dc | eu.eurocorp | DC del dominio hijo de eurocorp |

---

## Regla de Oro para Leer el Diagrama

> **Flecha = "esto me permite hacer/llegar a"**

Pregúntate siempre tres cosas antes de atacar un servidor:
1. **¿Qué flecha llega a este servidor?** → eso es lo que necesitas tener ANTES
2. **¿Qué técnica dice la flecha?** → eso es lo que ejecutas
3. **¿Qué flechas salen de este servidor?** → eso es lo que GANAS al comprometerlo

---

## Patrón de Privilegios por Tipo de Ataque

| Tipo de Ataque             | Usuario de Dominio | Local Admin     | Algo Extra                | Ejemplo                 |
| -------------------------- | ------------------ | --------------- | ------------------------- | ----------------------- |
| **Ataques a nivel de RED** | ✅ Cualquiera       | ❌ No necesitas  | ❌ Nada más                | Kerberoast, AD CS       |
| **Ataques LOCALES**        | ✅ Necesitas uno    | ✅ En ese server | ❌ Nada más                | GPO Abuse, Allowlisting |
| **Ataques ENCADENADOS**    | ✅ Necesitas uno    | ✅ Previo        | ✅ Creds/TGT robados antes | DCSync, Delegation      |

---

## Cadena de Ataque Completa

```
Student VM (VPN/Browser)
    │
    ├─[Kerberoast / AD CS]───────────────────────────────────────────────────→ dcorp-dc
    │                                                                               │
    ├─[Jenkins RCE]──→ dcorp-ci                                                    │
    │                       │                                                       │
    │               [Derivative Local Admin]                                        │
    │                       ↓                                                       │
    │               dcorp-adminsrv──[Constrained Deleg]────────────────────→ dcorp-dc
    │                                                                               │
    ├─[cladmin creds]──→ dcorp-mgmt──[RBCD + Unconstrained Deleg]──────────→ dcorp-dc
    │                                                                               │
    ├──→ dcorp-appsrv──[Constrained Deleg websvc + Unconstrained Deleg]────→ dcorp-dc
    │                                                                               │
    │                                                                    ┌──────────┴──────────┐
    │                                                                    ↓                     ↓
    │                                                                mcorp-dc           eurocorp-dc
    │                                                            (Forest moneycorp)   (Forest eurocorp)
    │                                                                                        │
    └──→ dcorp-mssql──[Abuse Database Links]────────────────────────────────→ eurocorp-dc / eu-sql
```

---

## Servidores y sus Ataques

### 🖥️ dcorp-ci (Jenkins)

| Ataque | Usuario de Dominio | Local Admin | Algo Extra | ¿Directo desde Student VM? |
|---|---|---|---|---|
| Reverse Shell via Jenkins | ❌ No necesitas | ❌ No necesitas | Solo acceso de red al puerto Jenkins | ✅ Sí |
| GPO Abuse | ✅ Necesitas uno | ✅ En dcorp-ci | Tener ya el Reverse Shell | ❌ Primero Jenkins |
| Evade Allowlisting by modifying GPO | ✅ Necesitas uno | ✅ En dcorp-ci | Tener ya el Reverse Shell | ❌ Primero Jenkins |

**Lo que GANAS al comprometer dcorp-ci:**
- Local Admin en dcorp-ci
- Derivative Local Admin hacia dcorp-adminsrv

---

### 🖥️ dcorp-adminsrv

| Ataque | Usuario de Dominio | Local Admin | Algo Extra | ¿Directo desde Student VM? |
|---|---|---|---|---|
| Abuse Constrained Delegation on dcorp-adminsrv$ | ✅ Necesitas uno | ✅ En dcorp-ci primero | TGT/credenciales de dcorp-ci | ❌ Primero comprometer dcorp-ci |
| Extract Credentials | ✅ Necesitas uno | ✅ En adminsrv | Haber abusado la delegación | ❌ Primero la delegación |

**Lo que GANAS al comprometer dcorp-adminsrv:**
- Credenciales para atacar dcorp-dc
- Acceso via Constrained Delegation al DC

---

### 🖥️ dcorp-mgmt

| Ataque                         | Usuario de Dominio | Local Admin                    | Algo Extra                       | ¿Directo desde Student VM?           |
| ------------------------------ | ------------------ | ------------------------------ | -------------------------------- | ------------------------------------ |
| ciadmin is local admin         | ✅ Necesitas uno    | ⚠️ ciadmin ya lo es por config | Conocer/tener creds de ciadmin   | ⚠️ Semi-directo, necesitas las creds |
| Abuse RBCD                     | ✅ Necesitas uno    | ✅ En dcorp-mgmt                | Haber usado cladmin              | ❌ Primero ciadmin                    |
| Abuse Unconstrained Delegation | ✅ Necesitas uno    | ✅ En dcorp-mgmt                | RBCD completado                  | ❌ Primero RBCD                       |
| Extract Domain Admin creds     | ✅ Necesitas uno    | ✅ En dcorp-mgmt                | Unconstrained Delegation abusado | ❌ Es el resultado final              |

**Lo que GANAS al comprometer dcorp-mgmt:**
- TGT de dcorp-dc$ y mcorp-dc$ via Unconstrained Delegation
- Domain Admin credentials

---

### 🗄️ dcorp-mssql

| Ataque | Usuario de Dominio | Local Admin | Algo Extra | ¿Directo desde Student VM? |
|---|---|---|---|---|
| Abuse Database Links | ✅ Necesitas uno | ❌ No necesitas | Acceso al SQL Server + DB Links configurados hacia eurocorp | ⚠️ Semi-directo, necesitas acceso al SQL |

> **¿Qué son Database Links?**
> SQL Server permite crear "links" entre bases de datos de diferentes servidores. Si dcorp-mssql tiene un link hacia un servidor en eurocorp, puedes ejecutar comandos en ese servidor remoto usando el link, saltando el trust de forest a forest sin necesitar credenciales de eurocorp directamente.

**Lo que GANAS al abusar dcorp-mssql:**
- Acceso a eurocorp-dc y eu-sql sin credenciales de eurocorp
- Posible ejecución de comandos en servidores de eurocorp.local

---

### 🖥️ dcorp-appsrv / dcorp-sql1

| Ataque | Usuario de Dominio | Local Admin | Algo Extra | ¿Directo desde Student VM? |
|---|---|---|---|---|
| appadmin is local admin | ✅ Necesitas uno | ⚠️ appadmin ya lo es por config | Conocer creds de appadmin | ⚠️ Semi-directo, necesitas las creds |
| Abuse Constrained Delegation websvc | ✅ Necesitas uno | ✅ En appsrv | Creds de appadmin | ❌ Primero appadmin |
| Abuse Unconstrained Delegation | ✅ Necesitas uno | ✅ En appsrv/sql1 | Constrained Delegation completada | ❌ Primero la delegación |

**Lo que GANAS al comprometer dcorp-appsrv/sql1:**
- TGTs via Unconstrained Delegation
- Movimiento lateral hacia dcorp-dc

---

### 🏰 dcorp-dc (Domain Controller dollarcorp)

| Ataque | Usuario de Dominio | Local Admin | Algo Extra | ¿Directo desde Student VM? |
|---|---|---|---|---|
| Kerberoast svcadmin | ✅ Cualquier usuario | ❌ No necesitas | Nada más | ✅ Sí, directo |
| Abuse AD CS | ✅ Cualquier usuario | ❌ No necesitas | Nada más | ✅ Sí, directo |
| DCSync using dcorp-dc$ TGT | ✅ Necesitas uno | ❌ No necesitas | TGT capturado via Unconstrained Deleg | ❌ Primero comprometer dcorp-mgmt |
| Access DC as svcadmin | ✅ svcadmin específico | ❌ No necesitas | Hash crackeado del Kerberoast | ❌ Primero crackear el hash |
| Abuse Constrained Delegation on dcorp-adminsrv$ | ✅ Necesitas uno | ❌ No necesitas | Credenciales de adminsrv | ❌ Primero comprometer adminsrv |

**Lo que GANAS al comprometer dcorp-dc:**
- Domain Admin en dollarcorp
- Acceso para atacar mcorp-dc (cross-forest moneycorp)
- Acceso para atacar eurocorp-dc (cross-forest eurocorp via Trust Key)
- Hash krbtgt de dcorp

---

### 🌲 mcorp-dc (Forest Root moneycorp)

| Ataque | Usuario de Dominio | Local Admin | Algo Extra | ¿Directo desde Student VM? |
|---|---|---|---|---|
| Abuse AD CS | ✅ De moneycorp | ❌ No necesitas | Ser DA en dcorp primero | ❌ Primero comprometer dcorp-dc |
| DCSync using mcorp-dc$ TGT | ✅ De moneycorp | ❌ No necesitas | TGT capturado + DA en dcorp | ❌ Primero comprometer dcorp-dc |
| Abuse Trust Key from dcorp | ✅ De moneycorp | ❌ No necesitas | Ser DA en dcorp + Trust Key | ❌ Primero comprometer dcorp-dc |
| Abuse krbtgt from dcorp | ✅ De moneycorp | ❌ No necesitas | Hash krbtgt de dcorp | ❌ Primero comprometer dcorp-dc |

**Lo que GANAS al comprometer mcorp-dc:**
- Enterprise Admin en el forest moneycorp
- Control total de moneycorp.local y sus dominios hijo

---

### 🌍 eurocorp-dc / eu-sql / eu-dc (Forest Externo)

| Ataque | Usuario de Dominio | Local Admin | Algo Extra | ¿Directo desde Student VM? |
|---|---|---|---|---|
| Abuse Trust Key from dcorp | ✅ De eurocorp | ❌ No necesitas | Ser DA en dcorp + Trust Key de eurocorp | ❌ Primero comprometer dcorp-dc |
| Abuse Database Links (via dcorp-mssql) | ✅ De dollarcorp | ❌ No necesitas | Acceso a dcorp-mssql con DB Links activos | ⚠️ Semi-directo via SQL Links |

> **Dos caminos hacia eurocorp:**
> 1. **Via Trust Key** → Comprometer dcorp-dc primero, luego abusar la confianza entre forests
> 2. **Via Database Links** → Abusar los links de SQL Server en dcorp-mssql (camino alternativo)

**Lo que GANAS al comprometer eurocorp:**
- Enterprise Admin en el forest eurocorp
- Control de eu-sql y eu-dc

---

## Orden Recomendado de Ataque

```
1. Student VM
      │
      ├─ En paralelo: Kerberoast svcadmin + Abuse AD CS (directo)
      │
      ▼
2. dcorp-ci  →  Jenkins RCE → GPO Abuse → Local Admin
      │
      ▼
3. dcorp-adminsrv  →  Derivative Local Admin → Extract Creds → Constrained Deleg
      │
      ▼
4. dcorp-dc  →  DA (Domain Admin) ← múltiples caminos convergen aquí
      │
      ├─────────────────────────────────────────────┐
      ▼                                             ▼
5. mcorp-dc (EA moneycorp)             eurocorp-dc (EA eurocorp)
                                              ↑
                          También via: dcorp-mssql → Database Links
```

---

## Técnicas Clave - Referencia Rápida

| Técnica | ¿Qué hace? | ¿Qué necesitas? |
|---|---|---|
| **Kerberoasting** | Roba hash de cuenta de servicio para crackear offline | Solo usuario de dominio |
| **AD CS Abuse** | Abusa de Certificate Services para obtener certificados de auth | Solo usuario de dominio |
| **Unconstrained Delegation** | Captura TGTs de cualquier usuario que se conecte al server | Ser admin en el server con esta config |
| **Constrained Delegation** | Impersona usuarios hacia servicios específicos | Credenciales de la cuenta con delegación |
| **RBCD** | Resource-Based Constrained Delegation, variante más flexible | Control sobre atributo msDS-AllowedToActOnBehalfOfOtherIdentity |
| **DCSync** | Simula un DC para pedir hashes de usuarios | Permisos de replicación (DA o equivalente) |
| **Trust Key Abuse** | Usa la clave de confianza entre dominios para moverse cross-forest | Ser DA + conocer la Trust Key |
| **Golden Ticket** | Forja TGTs usando el hash krbtgt | Hash krbtgt del dominio |
| **Database Links Abuse** | Ejecuta comandos en SQL Servers remotos via links entre DBs | Acceso al SQL Server origen con DB Links configurados |
