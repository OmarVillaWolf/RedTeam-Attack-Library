# Registros 

Tags: #Registers #Stack

Los registros de propósito general son usados para almacenar data temporalmente durante ejecución de un programa. Son versátiles y pueden mantener varios tipos de data, como enteros, dirección en memoria o resultados intermedios de aritmética/operadores lógicos. Algunos ejemplos de ellos son:
* EAX, EBX, ECX, EDX: Usados para la manipulación de data en general y operaciones aritméticas.
* ESI, EDI: A veces usados para operaciones de manipulación de string. 
* ESP, EBP: Usados para el manejo de stack: (ESP apunta al stack, EBP apunta a la base del stack)
* En arquitecturas de x64, los registros de propósito general son extendidos a 64 bits (RAX, RBX, RCX, RDX, etc...), provee un incremento de direcciones en espacio de memoria y soportando varios tipos de data. 

**EAX (Accumulator Register):**  Es un acumulador de registros usado para operaciones de aritmética y operadores lógicos.  A veces usado para almacenar operaciones y recibir resultados computados. En ciertos contextos, mantiene la función del valor de regreso y es usado como registro de cálculos intermedios. 
**EBX (Base Register):** Conocido como registro de base, es tipicamente usado como un apuntador a la memoria en data o como base de direcciones de memoria de las operaciones. Puede también servir como registro de propósito general para almacenar data temporalmente durante la computación. 
**EXC (Counter Register):** Es comúnmente usado para el control de ciclos y conteo de iteraciones. Es usado en conjunto con las instrucciones de LOOP para implementar ciclos y tareas repetitivas.  
**EDX (Data Register):** Es usado en conjunto con EAX para ciertas operaciones que requieren un gran rango de almacenamiento de data (64 bit multiplicación y división). También, puede servir como un registro de propósito general para almacenar data.
**ESI (Source Index Register):** Es comúnmente usado en operaciones de manipulación de string. Tipicamente mantiene el comienza de dirección de la data fuente o la fuente de string durante las operaciones como copiar, comparar o búsqueda de string. 
**EDI (Destination Index Register):** Complementa el registro ESI en operaciones de manipulación de string. Usualmente mantiene el comienzo de direcciones de la data de destino o del destino del string durante operaciones como copiar o concatenar. 
**ESP (Stack Pointer Register):** Puntos de la cima del stack en memoria. Es usado para manejar el stack, un área especial de memoria usada para almacenar parámetros de funciones, variables locales, direcciones y otra data durante la ejecución del programa. 
**EBP (Base Pointer Register):** Es comúnmente usado en conjunción con el registro de ESP para acceder a los parámetros y variables locales dentro de las llamadas de función.  Sirve como un punto de referencia para acceder a data almacenada en el stack. 

**Stack:** Es una estructura fundamental de data. El stack es la región de memoria alocada para almacenar las funciones de frames, variables locales, funciones de parámetros y direcciones de regreso durante la ejecución de programa. 