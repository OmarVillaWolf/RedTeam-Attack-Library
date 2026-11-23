# Beacons Payloads 

Tags: #CobaltStrike 

## Stomped loader 

Beacon se implementó originalmente como una DLL "reflexiva", basada en el [trabajo de Stephen Fewer](https://github.com/stephenfewer/ReflectiveDLLInjection).  Este tipo de DLL exporta una función, que se encarga de recargar su propia imagen en la memoria y realizar tareas como cargar módulos dependientes.  La sección 'descripción general' del repositorio de Stephen repasa los distintos pasos con más detalle.  La DLL de Beacon exporta una función llamada **ReflectiveLoader**, que cuando se llama, recorre su propia imagen, asigna una nueva copia de sí misma a la memoria y llama a su punto de entrada.  Debido a que la DLL se exporta como código shell, se debe escribir un pequeño fragmento de código shell sobre el encabezado DOS del PE.  Este shellcode es responsable de saltar la ejecución del código al ReflectiveLoader exportado, que se encuentra en la sección .text del PE.

![[Pasted image 20261122185431.png]]
## Prepended loader 

Desde Cobalt Strike 4.11, Beacon ha comenzado a utilizar un nuevo estilo de cargador basado en el utilizado por DoublePulsar.  Esto fue construido por la NSA y filtrado por el grupo de hackers The Shadow Brokers.  El cargador es famoso por su asociación con EternalBlue y WannaCry.

Este es un cargador "prepuesto", lo que significa que el cargador está antepuesto al frente de un PE en lugar de ser parte de él.  El siguiente diagrama de Fortra muestra la diferencia entre los dos estilos de cargador.

![[Pasted image 20261122185503.png]]

Un cargador prepuesto es en general más sigiloso y flexible que un cargador pisoteado.  No requiere que se escriba un fragmento de código shell sobre los encabezados DOS del PE; puede cargar reflexivamente cualquier PE; no requiere que el PE exporte ninguna función necesaria para el proceso de carga reflexiva; y ofrece más opciones para ofuscar el PE.  El PE en sí se puede codificar, cifrar, comprimir, etc., y seguirá funcionando siempre que el cargador sepa cómo manejar esas ofuscaciones.

### Staged vs stageless payloads 

Es común que los marcos de ataque, como Metasploit y Cobalt Strike, desacoplen los 'exploits' de las 'cargas útiles'. Imagine una aplicación que tiene una vulnerabilidad de ejecución de código debido a un desbordamiento del búfer: la 'explotación' es el código que desencadena el desbordamiento y la 'carga útil' es el código que desea que se ejecute como resultado (por ejemplo Código shell de Meterpreter o Beacon). El tamaño de la carga útil que puede entregar puede estar limitado por factores como la naturaleza de la vulnerabilidad en sí o el canal al que se puede acceder. Si eres mayor como yo, quizás recuerdes MS08-067, una infame vulnerabilidad de ejecución remota de código (RCE) en Windows. Se trataba de una vulnerabilidad de corrupción de pila que debía explotarse dentro de un límite de ~400 bytes.

Para combatir estas situaciones, se hizo popular la 'payload staging'. Consiste en un pequeño programa (el stager) que, cuando se ejecuta, busca la carga útil completa (el stage) a través de un canal diferente del exploit, como HTTP, la inyecta en una nueva región de memoria y le pasa la ejecución. La preparación de la carga útil es buena para evitar este tipo de restricciones de explotación, pero también tiene varias desventajas.

### Payload security 

Cuando se inicia por primera vez, un servidor del equipo Cobalt Strike generará un nuevo par de claves público/privado único, y cada carga útil sin etapas generada por ese servidor tendrá la clave pública del servidor incorporada. Beacon utiliza esta clave para cifrar sus metadatos cuando habla con el servidor del equipo, lo que garantiza que solo pueda comunicarse con el servidor del equipo desde el que se generó. Además, cada Beacon utiliza una clave de sesión única que se utiliza para cifrar/descifrar los datos de tareas/salida intercambiados con el servidor del equipo. Esa clave de sesión en sí se transmite dentro de los metadatos cifrados, de modo que solo el servidor del equipo correcto puede cifrar y descifrar el tráfico C2 de un Beacon. El resultado de esto es que no es fácil utilizar como intermediario una carga útil sin etapas para secuestrar su control. Sin embargo, debido a su pequeño tamaño, los escenarios no tienen la misma seguridad.Cuando se ejecuta, el stager se comunicará con el host/puerto con el que fue configurado para recibir el resto de la etapa de carga útil. No existe ninguna validación por parte del stager para garantizar que se está comunicando con un servidor de equipo legítimo, lo que los hace susceptibles de secuestro cuando se ejecutan por primera vez.

### OPSEC 

Para mantener los escalones lo más pequeños posible, deben omitir pasos que hoy en día podemos considerar como "buenas prácticas". Por ejemplo, las cargas útiles completas sin etapas evitan el uso de memoria RWX (lectura, escritura, ejecución) porque se destaca como anómala en la mayoría de los procesos de Windows. En lugar de ello, primero asignan la memoria como RW y luego la transfieren a RX antes de la ejecución. Los escenarios de carga útil no hacen esto porque requiere una llamada API adicional, que requiere más código. Los escenarios a menudo se construyen en un ensamblaje optimizado manualmente, por lo que cada byte que se puede guardar lo es.

Para dar una idea sobre la diferencia de tamaño, hoy en día, un escenario Beacon tiene ~890 bytes y un escenario Beacon completo tiene ~307200 bytes.  Eso es ~345 veces más grande.

## Generating payloads 

Las cargas útiles de las balizas se pueden generar desde el menú Cargas útiles.

### HTML application 

Esta opción genera una carga útil en`.hta` formato, que es una combinación de HTML y VBScript.  Este es un lenguaje de programación compatible con Internet Explorer, por lo que se convirtió en un formato de carga útil popular para ataques de phishing.  El cuadro de diálogo le solicita que seleccione un oyente y el método de ejecución.

![[Pasted image 20261122190200.png]]

Las opciones son:
* ``Executable`` : Suelte un exe stager en el disco y ejecútelo.
* ``PowerShell``: Utiliza powershell.exe para ejecutar un stager en la memoria.
* ``VBA``: Utiliza una macro VBA para ejecutar un stager en la memoria.  La macro se ejecuta utilizando VBScript para crear una instancia de Excel._Application_ y crear una instancia de libro de trabajo. Requiere que Microsoft Office esté instalado en el destino.

### MS Office macro 

Esta opción producirá una macro VBA que puedes pegar en un documento de Office, otro formato de carga útil de phishing popular.

![[Pasted image 20261122190357.png]]

Esta macro siempre ofrece una carga útil de Beacon x86.

### Stager payload generator 

Estos generadores de carga útil producen archivos de código fuente para una variedad de lenguajes, equivalentes a la utilidad de Metasploit`msfvenom`.  Estos son útiles cuando desea una matriz de bytes que pueda colocar en sus propios ejecutores de shellcode.

![[Pasted image 20261122190424.png]]

Por ejemplo, al generar un stager en C se produce lo siguiente:

```c++
/* length: 888 bytes */
unsigned char buf[] = "\xfc\x48\x83\xe4[...snip...]\x12\x1d\x9b\x09";
```

Las cargas útiles de Stager no le permiten especificar ningún detalle de configuración adicional, como barandillas.

### Stageless payload generator 

El generador de carga útil sin etapas es prácticamente idéntico al generador de carga útil con etapas, pero ofrece más opciones.

![[Pasted image 20261122190512.png]]

#### Función de salida

Esta configuración controla la API que recibe llamadas cuando se ejecuta el comando de Beacon `exit`.  El proceso   llama a  [ExitProcess](https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-exitprocess), que finalizará todo el proceso en el que se ejecuta Beacon; mientras que **Thread** llama a [ExitThread](https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-exitthread), que solo finaliza el hilo que ejecuta Beacon.  Las cargas útiles ExitProcess (xprocess) y ExitThread (xthread) tienen sus usos.  Si Beacon se inyecta en un proceso que ya se está ejecutando en un sistema de destino, utilice xthread.  Si se inyecta en un proceso ejecutado por usted mismo, utilice xprocess.[](https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-exitprocess)[](https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-exitthread)

#### Llamada al sistema

Esto le indica a Beacon que utilice llamadas al sistema en lugar de las API Win32 normales para su funcionamiento interno.  Las dos opciones son:

- **Direct** utiliza la versión Nt* de la función.
- **Sal** tos indirectos a la instrucción apropiada dentro de la versión Nt* de la función.

### Windows stager/stageless payload 

Estas opciones son similares a los generadores de carga útil stager/stageless, pero en lugar de generar archivos fuente sin procesar, proporcionan ejecutables prediseñados.  Las opciones de salida son:

- #### Windows EXE
    Un ejecutable estándar de Windows.
    
- #### Servicio de Windows EXE
    Un ejecutable de Windows diseñado específicamente para interactuar con el Administrador de control de servicios.
    
- #### DLL de Windows
    Una DLL que contiene varias exportaciones a las que se puede llamar mediante utilidades como`rundll32` y`regsvr32` .

Estas cargas útiles también se pueden firmar mediante un certificado de firma de código.

### Windows stageless Generate All Payloads 

Esta opción generará todas las posibles variantes de carga útil sin etapas para cada oyente disponible en arquitecturas x86 y x64.

![[Pasted image 20261122190716.png]]