## Bypassing PowerShell Security 

Tags: #AD #Bypass #AD #InvisiShell 

Los scripts de Invisi-Shell crean una sesión de PowerShell más sigilosa habilitando CLR profiling y cargando un profiler en memoria. Con el fin de ejecutar tools de AD con menor detección.

* [Invisi-shell](https://github.com/OmerYa/Invisi-Shell)

1.  Invisi-Shell permite ejecutar PowerShell con menor logging y visibilidad, ayudando a evadir mecanismos como 'Script Block Logging y ETW'.
2. No modifica archivos ni ensamblados en disco; realiza hooking en memoria de funciones internas de PowerShell.
3. Usa la CLR Profiling API para cargar un profiler que intercepta llamadas del runtime durante la ejecución.
4. El profiler del CLR es una DLL cargada en tiempo de ejecución, que permite interceptar funciones internas sin alterar el sistema.

```bash 
❯ RunWithPathAsAdmin.bat           # Ejecutar con privilegios de admin
❯ RunWithRegistryNonAdmin.bat      # Ejecutar sin privilegios


NOTA:
	1. Escribir 'Exit' desde la nueva sesión de Powershell para limpiar la consola 
```