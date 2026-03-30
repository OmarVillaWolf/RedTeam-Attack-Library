# Apagando defensas básicas

Tags: #AD #Windows #Evasion 

```bash 
1. Evitar la detección: Al desactivar la protección en tiempo real y otras características del antivirus, el atacante puede operar en el sistema sin ser detectado. Esto también le permite ejecutar o implantar herramientas y malware sin ser bloqueado por el software de seguridad.
    
2. Facilitar el movimiento lateral: Con las reglas del firewall desactivadas, es más fácil para el atacante moverse lateralmente a través de la red, conectar herramientas remotas o exfiltrar datos sin ser bloqueado por reglas de firewall.
    
3. Preparar el sistema para actividades adicionales: Al tener un control más libre del sistema, el atacante puede realizar actividades adicionales como establecer puertas traseras, recopilar información o realizar otras acciones maliciosas.
```

## Powershell 

```powershell
❯ Set-MpPreference -DisableIOAVProtection $true -Verbose       # Desactiva la protección contra malware durante la escritura de archivos a disco (protección IOAV)

❯ Set-MpPreference -DisableRealtimeMonitoring $true -Verbose   # Desactiva la supervisión en tiempo real del software antivirus, lo que significa que no escaneará y detectará actividades maliciosas en tiempo real

❯ Get-MpPreference | select DisableIOAVProtection, DisableRealtimeMonitoring  # Verifica y muestra el estado actual de las dos características anteriores (protección IOAV y supervisión en tiempo real)

❯ Set-NetFirewallProfile -name Domain,Private,Public -Enabled False -Verbose  # Desactiva las reglas del firewall para todos los perfiles (Dominio, Privado y Público)

❯ netsh advfirewall set allprofiles state off    # Es otro comando para desactivar todas las reglas del firewall para todos los perfiles utilizando la utilidad 'netsh'
```

