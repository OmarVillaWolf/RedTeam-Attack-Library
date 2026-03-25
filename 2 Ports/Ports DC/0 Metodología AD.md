# Metodología Active Directory

Tags: #AD #Metodologia #DC #Flujo

## OBJETIVO
- Tener el mapa claro de ataque desde cero hasta Domain Admin.
- Saber exactamente qué hacer en cada fase según lo que tengas.
- Los comandos están en las notas individuales de cada puerto.

## TIPS
1. **Enumera más de lo que crees necesario antes de atacar**
2. **Cada credencial nueva → pruébala en TODOS los servicios inmediatamente**
3. **Anota cada usuario, hash y ticket que encuentres → todo se reutiliza**
4. **Si te atascas → vuelve a enumerar, no a atacar más fuerte**
5. **BloodHound primero cuando tengas creds → te dice el camino antes de buscarlo a ciegas**

---

## FASE 1 — RECONOCIMIENTO (SIN CREDS)

**Objetivo:** Confirmar que es un DC, obtener el nombre de dominio y agregar al /etc/hosts

Puertos que confirman que es un DC:
- 88 Kerberos → confirmación principal
- 389 LDAP
- 445 SMB
- 3268 Global Catalog

Pasos:
1. Escaneo de puertos completo
2. Identificar hostname y dominio vía SMB (nxc)
3. Agregar dominio al /etc/hosts → obligatorio para Kerberos y LDAP
4. **Sincronizar reloj con el DC → SIEMPRE antes de cualquier ataque Kerberos**

---

## FASE 2 — ENUMERACIÓN SIN CREDENCIALES

**Objetivo:** Conseguir usuarios válidos o credenciales para pasar a Fase 3

Intentar en este orden:

1. **SMB null session**
   - ¿Hay shares accesibles sin creds?
   - → Sí: buscar archivos con credenciales → ir a Fase 3
   - → No: continuar

2. **RPC null session**
   - ¿Puedo enumerar usuarios y grupos?
   - → Sí: guardar lista de usuarios → continuar con Kerbrute
   - → No: continuar

3. **LDAP anonymous bind**
   - ¿Devuelve usuarios o estructura del dominio?
   - → Sí: guardar usuarios → continuar con Kerbrute
   - → No: continuar

4. **Kerbrute con wordlist**
   - Validar usuarios vía Kerberos → más sigiloso que LDAP/RPC
   - Resultado: lista de usuarios válidos confirmados

5. **AS-REP Roasting** ← ejecutar siempre con la lista de usuarios
   - ¿Algún usuario no requiere preautenticación?
   - → Sí: obtener hash → crackear offline → si crackeas → ir a Fase 3
   - → No: intentar password spraying con contraseñas comunes

> **Si agotaste la Fase 2 sin creds → revisar puerto 80/443, otras apps expuestas**

---

## FASE 3 — CON CREDENCIALES VÁLIDAS (user:pass)

> Ejecutar este bloque completo cada vez que obtengas credenciales nuevas

**Objetivo:** Entender qué puedes hacer con esas creds y encontrar el camino a DA

1. **Validar en todos los servicios**
   - SMB → ¿admin local? ([Pwn3d!])
   - WinRM → ¿puedo conectarme?
   - LDAP → ¿tengo acceso?
   - MSSQL → ¿está abierto?

2. **BloodHound** ← ejecutar antes de cualquier ataque
   - Recopilar toda la información del dominio
   - Queries clave:
     - Shortest Path to Domain Admins
     - Find Principals with DCSync Rights
     - Shortest Path from Owned Principals
     - Find AS-REP Roastable Users
     - Find Kerberoastable Users
   - **BloodHound te dice el camino → no ataques sin verlo primero**

3. **Kerberoasting** ← con cualquier cuenta de dominio
   - ¿Hay cuentas de servicio con SPNs?
   - → Sí: obtener hashes → crackear offline → si crackeas → nueva cuenta → repetir Fase 3

4. **Enumerar más**
   - Usuarios y grupos del dominio
   - Política de contraseñas → define agresividad del spraying
   - Usuarios logueados actualmente → objetivo para movimiento lateral
   - Cuentas con delegación → alto valor
   - Shares accesibles con estas creds

---

## FASE 4 — ESCALADA EN EL DOMINIO

> BloodHound ya te dijo el camino — aquí solo ejecutas lo que te indicó

Rutas más comunes según lo que encuentres:

- **ACL Abuse** → tienes GenericAll / WriteDACL / ForceChangePassword sobre alguien
  → Cambiar su contraseña → nueva cuenta comprometida → repetir Fase 3

- **Kerberoasting** → crackeaste hash de cuenta privilegiada
  → Nueva cuenta → repetir Fase 3

- **DCSync** → tienes GetChanges + GetChangesAll
  → Dump de todos los hashes → hash de Administrator → Fase 5

- **Admin local comprometido** → [Pwn3d!] en nxc
  → Dump de SAM/LSA → reutilizar hashes en otros hosts

---

## FASE 5 — DA COMPROMETIDO

1. Dump de todos los hashes del dominio (DCSync / NTDS)
2. Pass-the-Hash con hash de Administrator → acceso a todos los hosts
3. Documentar la cadena completa de ataque para el reporte
4. Buscar flags (proof.txt / local.txt)

---

## FLUJO DE DECISIÓN RÁPIDO

```
¿Tienes creds?
│
├── NO → Fase 2
│         ├── Null session SMB/RPC/LDAP → usuarios
│         ├── Kerbrute → usuarios válidos
│         └── AS-REP Roasting → hash → crackear
│                   └── ¿Crackeaste? → SÍ → Fase 3
│                                    → NO → password spraying / revisar otras apps
│
└── SÍ → Fase 3
          ├── Validar en todos los servicios
          ├── BloodHound → ver el camino
          ├── Kerberoasting → más hashes
          └── Seguir path de BloodHound → Fase 4
                    ├── ACL Abuse → nueva cuenta → repetir Fase 3
                    ├── DCSync → hash Administrator → Fase 5
                    └── Admin local → dump SAM → PTH en otros hosts
```

---

## CHECKLIST PARA EL EXAMEN

```
[ ] Escaneo completo de puertos
[ ] Identificar hostname y dominio
[ ] Agregar dominio a /etc/hosts
[ ] Sincronizar reloj con ntpdate
[ ] Null session SMB → shares y usuarios
[ ] Null session RPC → usuarios y grupos
[ ] Anonymous LDAP → estructura del dominio
[ ] Kerbrute → validar usuarios
[ ] AS-REP Roasting → sin creds
[ ] ¿Tengo creds? → validar en todos los servicios
[ ] BloodHound → antes de atacar
[ ] Kerberoasting → con cualquier cuenta válida
[ ] Seguir path de BloodHound → no atacar a ciegas
```

---

## PUERTOS CLAVE DE UN DC

| Puerto    | Servicio       | Nota                     |
| --------- | -------------- | ------------------------ |
| 53        | DNS            | Ver nota 53_DNS          |
| 88        | Kerberos       | Ver nota 88_Kerberos     |
| 135       | RPC            | Ver nota 445_135_SMB_RPC |
| 139/445   | SMB            | Ver nota 445_135_SMB_RPC |
| 389/636   | LDAP/LDAPS     | Ver nota 389_LDAP        |
| 3268/3269 | Global Catalog | Ver nota 389_LDAP        |
| 5985/5986 | WinRM          | Ver nota 5985_WinRM      |
| 1433      | MSSQL          | Ver nota 1433_MSSQL      |
