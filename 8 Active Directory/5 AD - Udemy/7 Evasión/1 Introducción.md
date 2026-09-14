# Introducción a la detección 

Tags: #AD #Evasion 

## Static Signature Analysis

- Se fundamenta en el concepto de lista negra. Cuando un nuevo malware es identificado, los analistas crean unas firmas para detectarlo. Las firmas suelen basarse en los primeros bytes del binario para identificarlo. El problema de este enfoque es que no puede detectar nuevo malware o modificaciones sobre malware existente. 

## Static Heuristic Analysis 

- Se fundamenta en el concepto de lista negra. Se crean reglas que verifican patrones que normalmente se encuentran en malware. Estos patrones involucran artefactos como llamadas al sistema, proceso, escrituras en memoria... Permiten identificar malware nuevo o modificado. 

## Dynamic Analysis 

- Cuando se escanea un malware, se ejecuta en un entorno virtual durante u periodo de tiempo. Se combina con las técnicas anteriores y permite detectar malware nuevo, modificado o que se encuentre cifrado. Consume mucho más tiempo y recursos que las técnicas anteriores y no suele realizarse a no ser que se indique explícitamente. 

## Tipos de herramientas de seguridad 

| **Red**                              | **Endpoint**                          |
| ------------------------------------ | ------------------------------------- |
| Balanceadores de carga               | Antivirus                             |
| WAF (Web Application Firewall)       | EDR (Endpoint Detection and Response) |
| Network Firewall                     | DLP (Data Loss Prevention)            |
| IDS/IPS                              | HIDS                                  |
| NDR (Network Detection and Response) | Local Firewall                        |
| Sistema de detección de anomalías    | ...                                   |
