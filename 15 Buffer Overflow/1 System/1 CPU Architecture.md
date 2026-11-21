# Arquitectura del CPU

Tags: #Assembly 

Lenguaje de **ensamblador** es una puerta para entender las técnicas de explotación, ingeniería reversa, shellcoding y otro campos a bajo nivel en la seguridad de la información. 

**CU (Control Unit):** Es responsable de coordinar y controlar las operaciones del CPU. Busca/recupera instrucciones de la memoria, las decodea y maneja la ejecución de instrucciones redirigiendo el movimiento de la data y flujo de control dentro del CPU.
**ALU (Arithmetic Logic Unit):** Es el componente responsable de ejecutar operaciones lógicas y aritméticas.  Puede ejecutar operaciones básicas como adicción, sustracción, multiplicación, división así como operadores lógicos como AND, OR y NOT.
**Registers:** Son pequeños, almacenan locaciones a alta velocidad dentro del CPU usado para almacenar data temporalmente durante el proceso. Algunos tipos de registros incluyen:
	PC (Program counter): Mantiene las direcciones de memoria de la siguiente instrucción a ser buscada/recuperada.
	IR (Instruction Register): Mantiene la instrucción actual ejecutada.  
	Accumulator: Almacena el resultado de las operaciones lógicas y aritméticas. 
	General-Purpose Registers: Usado para almacenar valores intermedio durante la ejecución de la instrucción. 
