# Cross-Session Activate 

Tags: #Windows #AD #Powershell #qwinsta #DCOMRunAs #CRTE 

DLL = **Dynamic Link Library** (biblioteca de enlace dinámico). Piensa en un programa de Windows (un `.exe`) como un cocinero, y las DLLs como **recetarios compartidos**.

En vez de que cada programa traiga su propio código para todo (abrir ventanas, leer archivos, conectarse a la red), Windows guarda ese código común en archivos `.dll`. Cuando un `.exe` necesita una función, **la pide prestada** de la DLL correspondiente. Por eso muchos programas distintos pueden usar la misma DLL: no repiten código, lo comparten.

Aquí está la clave: **cuando un programa pide una DLL, Windows la busca en un orden específico de carpetas** (el “DLL Search Order”). A grandes rasgos busca primero en:

1. La carpeta del propio `.exe`.
2. Carpetas del sistema (`System32`, etc.).
3. Otras rutas del `PATH`.

El ataque, llamado **DLL Hijacking**, aprovecha un hueco: si un programa **privilegiado** (que corre como SYSTEM o como un admin) pide una DLL que **no existe** en el primer lugar donde Windows la busca, y tú **tienes permiso de escritura** en esa carpeta, puedes dejar ahí **tu propia DLL con el mismo nombre**.

Para que un DLL hijacking funcione necesitas las dos juntas:
1. **Un proceso con más privilegios que tú** que cargue una DLL.
2. **Permiso de escritura** en una carpeta donde ese proceso busque la DLL _antes_ de encontrar la legítima.


```powershell 
Paso 1:
# Listar las sesiones en el server remoto 
❯ qwinsta /server:<hostname>
```

```powershell 
Paso 2: DLL Hijacking
# Construir una VERSION.dll “proxied” (que reenvía sus funciones) y colocarla en “C:\Program Files\Windows Photo Viewer” en el host objetivo

Paso 3:
# Ejecutar el siguiente comando ejecutará nuestra DLL en la sesión del usuario objetivo
❯ DCOMRunAs.exe <session id> 00F2B433-44E4-4D88-B2B0-2698A0A91DBA <hostname>
	# El GUID 000F2B433-... es el AppID/CLSID de un objeto DCOM que en su configuración tiene RunAs = Interactive User
```