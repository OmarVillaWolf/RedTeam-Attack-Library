# NetBIOS (137 / 138 / 139)

Tags: #NetBIOS #Windows #DC #Enumeracion #NullSession #SMB

## OBJETIVO

- Obtener hostname y nombre de dominio/workgroup
- Enumerar recursos compartidos y usuarios vía NetBIOS
- Identificar el rol del host en la red (DC, workstation, etc.)
- Aprovechar null sessions para obtener información sin credenciales

## TIPS

1. **NetBIOS casi siempre va de la mano con SMB → si ves 139, busca 445 también**
2. **nmblookup y nbtscan → enumeración rápida sin creds de toda una red**
3. **Null session en 139 → puede dar acceso a usuarios y shares sin autenticación**
4. **El sufijo NetBIOS revela el rol del host → ver tabla de sufijos abajo**
5. **En entornos modernos 139 coexiste con 445 → 445 tiene prioridad pero 139 sigue siendo útil**

## TOOLS

- [NetExec](https://github.com/Pennyw0rth/NetExec)
- [nbtscan](http://www.unixwiz.net/tools/nbtscan.html)

---

## REFERENCIA DE SUFIJOS NETBIOS

|Sufijo|Tipo|Descripción|
|---|---|---|
|<00>|UNIQUE|Nombre del hostname del host|
|<00>|GROUP|Nombre del dominio o workgroup|
|<03>|UNIQUE|Servicio de mensajería del usuario|
|<1B>|UNIQUE|Domain Master Browser → indica DC|
|<1C>|GROUP|Domain Controllers del dominio|
|<1D>|UNIQUE|Master Browser de la subred|
|<20>|UNIQUE|File Server Service activo → SMB disponible|

### Insight

- `<1B>` → confirma que el host es el DC principal del dominio
- `<20>` → File Server activo → ir directamente a enumerar SMB en puerto 445

---

## 1. RECONOCIMIENTO INICIAL (SIN CREDENCIALES)

```bash
❯ nmblookup -A <IP>
# No requiere creds → obtiene tabla NetBIOS del host remoto
# Devuelve: hostname, dominio/workgroup, rol del host y MAC address
# Buscar sufijo <1B> para confirmar DC y <20> para confirmar SMB activo

❯ nbtscan <IP>
# No requiere creds → escaneo rápido de un host
# Devuelve hostname y workgroup

❯ nbtscan <rango/CIDR>
# No requiere creds → escanea toda una subred buscando hosts NetBIOS
# Ejemplo: nbtscan 10.10.10.0/24
# Útil para descubrir todos los hosts Windows de la red rápidamente

❯ nbtscan -r <rango/CIDR>
# No requiere creds → escaneo recursivo del rango
# Más completo que sin -r
```

### Insight

- nbtscan en un rango → mapa rápido de todos los hosts Windows antes de hacer nmap
- Hostname obtenido → agregar al /etc/hosts inmediatamente

---

## 2. ENUMERACIÓN DE NOMBRE Y DOMINIO

```bash
❯ nmblookup -A <IP>
# No requiere creds → resolución inversa NetBIOS
# Ejemplo de output:
# HOSTNAME        <00> - hostname del equipo
# DOMAIN          <00> - nombre del dominio o workgroup
# HOSTNAME        <20> - File Server activo
# DOMAIN          <1B> - Domain Master Browser (es DC)

❯ nbtstat -a <IP>
# Desde Windows → obtener tabla NetBIOS del host remoto
# Equivalente a nmblookup desde Windows

❯ nbtstat -c
# Desde Windows → ver caché NetBIOS local
# Muestra nombres resueltos recientemente → revela hosts con los que ha comunicado
```

---

## 3. RESOLUCIÓN DE NOMBRES NETBIOS

```bash
❯ nmblookup <hostname>
# No requiere creds → resolver hostname NetBIOS a IP
# Útil cuando tienes el nombre pero no la IP

❯ nmblookup -S <hostname>
# No requiere creds → resolver y mostrar servicios del host

❯ nmblookup '*'
# No requiere creds → buscar todos los hosts NetBIOS en la red local
# Hace broadcast → solo funciona en la misma subred
```

---

## CONDICIONES CLAVE

- Sin creds → nmblookup + nbtscan + null session
- Sufijo <1B> → confirma DC
- Sufijo <20> → SMB activo → ir a nota 445_SMB_RPC

## ONE-LINERS MENTALES

- Puerto 139 abierto → nmblookup -A IP para identificar el host
- nbtscan en rango → mapa rápido de todos los hosts Windows
- Sufijo <20> en output → File Server activo → ir a SMB
- Sufijo <1B> en output → es el DC → agregar al /etc/hosts
