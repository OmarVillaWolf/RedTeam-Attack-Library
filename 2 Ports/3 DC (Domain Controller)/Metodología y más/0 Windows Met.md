---
tags:
  - OSCP
  - Active-Directory
  - PrivEsc
---

# Checklist de Active Directory y escalada de privilegios

## Active Directory

- [ ] Escanear todos los puertos TCP.
- [ ] Comprobar LDAP, RPC y SMB con acceso anónimo. Revisar si hay recursos compartidos públicos.
- [ ] Buscar nombres de usuario. Si consigues acceso, buscar usuarios con herramientas como `netexec --rid-brute`, `--users` y `enumdomuser` con `rpcclient` para obtener la lista de usuarios del dominio sin credenciales iniciales. Comprobar los nombres de usuario con Kerbrute.
- [ ] Probar AS-REP roasting después de recopilar nombres de usuario. Si funciona, intentar descifrar los hashes.
- [ ] Comprobar Kerberoasting cuando tengas un nombre de usuario y una contraseña.
- [ ] Probar la autenticación con las credenciales disponibles en todos los protocolos posibles: WinRM, RDP, MSSQL, SMB, RPC, LDAP, etc.
- [ ] Enumerar los recursos compartidos a los que cada usuario tenga acceso. Cada usuario nuevo es motivo para volver a revisar los recursos compartidos.
- [ ] Si consigues acceso como usuario, buscar privilegios y permisos, y volcar los hashes. Guardarlos en un archivo para intentar descifrarlos por fuerza bruta y usarlos para movimiento lateral.
- [ ] Ejecutar BloodHound y revisar rutas de ataque, roasting y DCSync.
- [ ] Comprobar ataques basados en certificados con Certipy.
- [ ] Comprobar si un recurso compartido con permisos de escritura permite robar hashes con Responder.
- [ ] Si la red AD tiene adaptadores de red adicionales, comprobar si hace falta pivotar. Si es así, configurar un pivote y enrutar las herramientas a través de ese «jumpbox» para alcanzar las otras máquinas de la red.
- [ ] Recordar probar fuerza bruta contra distintos protocolos usando las credenciales disponibles. Incluir también autenticación NTLM.
- [ ] Para postexplotación, volcar todos los hashes con Remote Hashdump e intentar descifrarlos.
- [ ] Comprobar los puertos UDP.
- [ ] Si te atascas, volver a escanear y verificar que las herramientas funcionen correctamente y se estén ejecutando como esperas.

## Escalada de privilegios en Windows

- [ ] Ejecutar `whoami /all`, después PowerUp y luego WinPEAS.
- [ ] Guardar la salida de estas herramientas y revisarla con calma.
- [ ] Revisar también los puertos en escucha por si necesitas hacer port forwarding.
- [ ] Si te atascas por completo, revisar manualmente elementos como las credenciales y probar otras herramientas de escalada de privilegios. Si eso no ayuda, repasar las técnicas de escalada y considerar si realmente hace falta escalar privilegios.
