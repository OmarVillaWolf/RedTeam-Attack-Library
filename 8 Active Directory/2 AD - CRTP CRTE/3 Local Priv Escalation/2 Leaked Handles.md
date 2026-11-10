# Local Privilege Escalation -  Filtración de handles

Tags: #AD #Windows #Powershell #PrivEsc 


- **Handles de Windows**
    - Son referencias que un proceso usa para acceder a objetos internos del sistema, como procesos, hilos o tokens.
    - Se guardan en una tabla de handles propia de cada proceso.
    - Son una vía habitual para que los programas accedan a objetos protegidos del núcleo de Windows.

- **Herencia de handles**
    - Si un proceso se crea con `bInheritHandles = TRUE`, los handles marcados explícitamente como heredables se copian a la tabla del proceso hijo.
    - Al heredarlos, no se vuelven a comprobar los límites de privilegios.
    - La herencia puede ser útil para la comunicación entre procesos (IPC), pero es riesgosa en servicios con privilegios elevados.

- **Filtración de handles**
    - Un proceso `SYSTEM` podría exponer handles privilegiados mediante herencia o una creación insegura de procesos.
    - Un proceso hijo con pocos privilegios recibe esos handles con sus permisos de acceso originales.
    - Si el handle filtrado permite más acceso del necesario, el proceso de pocos privilegios podría usarlo para intentar elevar sus privilegios localmente.

**Idea clave:** un handle funciona como una llave de acceso a un objeto del sistema. Si un servicio privilegiado entrega o deja heredar una llave demasiado poderosa, el proceso que la recibe puede tener acceso indebido.

## Diagrama

![[Pasted image 20261109181633.png]]

Pasos:
1. **`HandleService.exe` obtiene una copia del token de `winlogon.exe`.**  
    `Winlogon.exe` es un proceso legítimo de Windows que suele ejecutarse con privilegios altos, como `SYSTEM`. Si otro programa consigue un handle con acceso suficiente a su token, puede ser un problema de seguridad.
2. **El servicio crea un proceso hijo de pocos privilegios.**  
    La flecha dice “Duplicate SYSTEM Token”, pero el dibujo no detalla exactamente cómo ocurre ni qué permisos tiene el handle. La idea general es que el servicio privilegiado tiene acceso a un token de `SYSTEM`.
3. **El proceso de pocos privilegios recibe un handle filtrado hacia ese token.**  
    Aunque el usuario del proceso sea de pocos privilegios, ese handle podría conservar permisos concedidos cuando se creó. Por eso el proceso podría intentar usarlo para iniciar otro proceso con una identidad más privilegiada.
4. **El ejemplo termina con `cmd.exe` ejecutándose como ``SYSTEM``.**  
    Eso representa la escalada conseguida.

**La herencia o filtración de un handle a un token privilegiado puede permitir que un proceso de bajos privilegios lo use para iniciar otro proceso con permisos elevados. El resultado depende de los derechos del handle, la configuración del token y las comprobaciones de Windows; el diagrama simplifica esos detalles.**


## Ejemplo 

![[Pasted image 20261109190045.png]]

**Process Hacker**
- Ejecutable que se está ejecutando como usuario con pocos privilegios.
- Tiene un handle hacia un proceso `SYSTEM` o de privilegios altos, marcado como heredable (indicado por el color cian).
- El handle tiene una máscara de acceso demasiado amplia, por ejemplo `PROCESS_ALL_ACCESS` o `PROCESS_CREATE_PROCESS`.

**Interpretar:**
- `colorcpl.exe` aparece ejecutándose con el usuario `US\studentuser2`, que la diapositiva presenta como un usuario de pocos privilegios.
- En la pestaña de handles de ese proceso, aparece una fila para `winlogon.exe`.
- Al abrir las propiedades del handle, la captura indica **“Granted access: 0x1fffff (Full control)”**: el handle tiene acceso amplio al objeto `winlogon.exe`.


## LeakedHandlesFinder

- `LeakedHandlesFinder.exe` enumera los handles filtrados que encuentra en el sistema.
- Automatiza el intento de aprovechar esos handles para iniciar procesos con privilegios elevados.

![[Pasted image 20261109190311.png]]


**Interpretar:**
La primera salida identifica un handle hacia un proceso con PID 692 (`winlogon.exe` en las diapositivas anteriores) y muestra `Granted access: 0x1fffff`, que la herramienta interpreta como acceso amplio. Lo etiqueta como **“Exploitable Handle”**.

En la segunda captura se ejecuta la herramienta con la opción `--cmd.exe`. La salida indica que creó un proceso y que el intento tuvo éxito; en la ventana de comandos, `whoami` devuelve `nt authority\system`. Eso significa que, en ese entorno de demostración, el símbolo del sistema terminó ejecutándose como `SYSTEM`.