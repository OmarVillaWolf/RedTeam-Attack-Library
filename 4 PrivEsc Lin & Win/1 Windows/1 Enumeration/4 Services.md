# Enumeración local de Windows 

Tags: #LocalEnumeration #PrivEsc #Windows #Meterpreter

## Enumeración de procesos y servicios 

### Que estamos buscando?

1. Procesos y servicios que estan corriendo
2. Tareas agendadas

## Comandos Windows 

```bash 
❯ net start                        # Muestrta los servicios de Windows que estan iniciados en el background

❯ wmic service list brief          # Lista todos los servicios que estan corriendo en el background, PID, State, Status

❯ tasklist /SVC                    # Lista los servicios que estan corriendo en un proceso en particular 

❯ schtasks /query /fo LIST /v      # Muestra una lista entera de tareas agendadas configuradas en el SO 
```

## Comandos con Meterpreter 

```bash 
❯ ps                     # Lista los procesos que estan corriendo, PID, Name, User, Path
❯ pgrep <name.exe>       # Para buscar un proceso en particular (PID = Process ID)
	❯ pgrep explorer.exe
	❯ pgrep hfs.exe
	❯ aws.exe

❯ migrate <PID>          # Migras al PID, cambiando de proceso puedes cambiar de arquitectura a x64
```

