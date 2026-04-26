# Introducción a EDR - MDE 

Tags: #AD #Bypass 

## MDE - Credential Extraction - LSASS Dump using Custom APIs

**MiniDumpDotNet:**

- Reimplementa `MiniDumpWriteDump` desde cero — no llama a la API de Windows
- Basado en código de ReactOS y un BOF (Beacon Object File) de Cobalt Strike
- El resultado es el mismo (dump de LSASS) pero sin usar la API vigilada
- Mucho más difícil de detectar para AV/EDR

* [MiniDumpDotNet](https://github.com/WhiteOakSecurity/MiniDumpDotNet)

```powershell 
! Usuario: Administrador local o Domain Admin

❯ .\minidumpdotnet.exe <LSASS PID> <minidump file>
# Volcar LSASS a un archivo usando una reimplementación custom de MiniDumpWriteDump — evasión de AV/EDR al no llamar la API de Windows vigilada. Requiere el PID de LSASS obtenido previamente con: Get-Process lsass

❯ tasklist /v  
# Usar este comando para enumerar el LSASS PID puede ser detectado por MDE, se recomienda hacer lo siguiente:  
```

```c
// Find PID of a process by name
int FindPID(const char* procname)
{
    int pid = 0;
    PROCESSENTRY32 proc = {};
    proc.dwSize = sizeof(PROCESSENTRY32);
    HANDLE snapshot = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);
    bool bProc = Process32First(snapshot, &proc);
    while (bProc)
    {
        if (strcmp(procname, proc.szExeFile) == 0)
        {
            pid = proc.th32ProcessID;
            break;
        }
        bProc = Process32Next(snapshot, &proc);
    }
    return pid;
}


# Cómo funciona paso a paso:
1. `CreateToolhelp32Snapshot` → toma una foto de todos los procesos corriendo en el sistema en ese momento
2. `Process32First` → se posiciona en el primer proceso de la lista
3. `while(bProc)` → itera por todos los procesos uno por uno
4. `strcmp(procname, proc.szExeFile)` → compara el nombre buscado con cada proceso — cuando encuentra `lsass.exe` retorna su PID
5. `Process32Next` → avanza al siguiente proceso si no lo encontró
```


## Tools Transfer and execution 

- Ahora que tenemos un par de ejecutables, vamos a transferirlos al objetivo.
- Descargar herramientas vía **HTTP(S)** puede ser riesgoso, ya que incrementa el puntaje de riesgo y las probabilidades de detección por el **EDR**.
- Sin embargo, si en el objetivo existen binarios diseñados para descargas como **Edge (msedge.exe)**, podemos realizar descargas HTTP(S) sin ser detectados.
- Otra forma más amigable a nivel **OPSEC** es compartir archivos mediante **SMB**. La ejecución puede realizarse directamente desde un recurso compartido con permisos de lectura y es menos riesgosa que las acciones típicas de descargar y ejecutar.

## Breaking Detection Chains 

- Según nuestra experiencia, la mayoría de los **EDR** correlacionan la actividad dentro de un intervalo de tiempo específico, después del cual se reinicia; esto varía según cada EDR.
- Para evadir estas detecciones basadas en correlación, podemos:
    - Intentar esperar un pequeño intervalo de tiempo (~10 minutos) antes de ejecutar la siguiente consulta.
    - Intercalar consultas no sospechosas entre las consultas sospechosas para romper las cadenas de detección.
- Ejecutaremos consultas SQL simples en el servidor **eu-sql**.

## Lateral Movement - ASR Rules Bypass 

- Las reglas **ASR (Attack Surface Reduction)** son fáciles de entender. Por ejemplo, la función **GetMonitoredLocations** muestra los procesos que están siendo monitoreados, y la ejecución remota utilizando estos resultará en una detección. 
- Métodos confiables del sistema operativo como **WMI** y **PowerShell Remoting (PsRemoting)**, o herramientas administrativas como **PsExec**, son detectadas por **MDE (Microsoft Defender for Endpoint)**.
- Para evitar detecciones basadas en una regla específica de ASR como **"Block process creations originating from PSExec and WMI commands"**:
    - Podemos usar alternativas como acceso por **WinRM (winrs)** en lugar de ejecución con PSExec/WMI _(esto no es detectado por MDE, pero sí por MDI)_.
    - Usar la función **GetCommandLineExclusions**, que muestra una lista de exclusiones en la línea de comandos (por ejemplo:  
        `.*\\windows\\ccm\\systemtemp\\.*`), ya que si se incluye en la línea de comando, puede permitir evadir esta regla y la detección.

- Una vez que tenemos acceso remoto a una máquina, podemos usar comandos como **whoami.exe** para la enumeración inicial.
- Dado que **whoami.exe** normalmente no se ejecuta bajo procesos como **sqlservr.exe**, es probable que genere una detección.
- Una forma más amigable a nivel **OPSEC** es usar alternativas como **`SET USERNAME`**, que realiza la misma función que **whoami.exe**, permitiendo obtener el usuario actual mediante variables de entorno.