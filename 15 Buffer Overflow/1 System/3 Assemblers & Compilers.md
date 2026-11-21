# Ensamblador 

Tags: #Assembly

Ensamblador es un lenguaje de programación. También, es un tipo de lenguaje que convierte el código del lenguaje ensamblador en código maquina el cual es directamente ejecutable en el CPU de la computadora. Ensamblador es un lenguaje que usa instrucciones  mnemotecnias , haciéndolo fácil para que los humanos puedan leer y escribir comparado al código binario. 

Tipos de ensambladores dependiendo el target:
1. Microsoft Macro Assembler (MASM), x86 usa syntax Intel para MS-DOS y Microsoft Windows. 
2. GNU Assembler (GAS), usado por el proyecto GNU, defecto back-end de GCC.
3. Netwide Assembler (NASM), x86 usado para escribir 16 bits, 32 bits (IA-32) y 64 bits (x86-64), uno de los mas populares ensambladores para Linux.
4. Flat Assembler (FASM), x86, soporta estilo Intel lenguaje ensamblador en IA-32 y x86-64. 

Un compilador es una herramienta de software usada en un programa de computadora para traducir el código fuente escrito en un alto nivel de lenguaje de programación dentro del código maquina o código ejecutable que puede ser directamente ejecutado por la CPU de la computadora. 

```bash 
❯ https://syscalls.w3challs.com/?arch=x86    # Lista de las llamadas en Linux 'aex'

❯ apt install nasm build-essential -y        # Descargamos el compilador 
❯ nano HelloWorld.asm                        # Creamos el archivo con extension de ensamblador 
```

```bash 
; Hello World                            # Escribimos un comentario en ensamblador con ;

section .data 
	hello db 'Hello, World!',0xA        ; Null terminated string

section .text 
	global _start                       ; Entry Point For the program  

_start:
	mov eax, 0x4                        ; Move system call number for sys_write to eax
	mov ebx, 0x1                        ; File descriptor 1 (stdout)
	mov ecx, hello                      ; Pointer of the string 
	mov edx, 13                         ; Length of the string 'Hello, World!'
	int 0x80                            ; Call Kernel
	
	; Gracefully Exit
	mov eax, 0x1                        ; System call number sys_exit
	xor ebx, ebx                        ; Return status 0
	int 0x80                            ; Call Kernel 
```

```bash 
❯ nasm -f elf32 -o hello_world.o HelloWorld.asm     # Creamos el objeto con '-o' y lo compilamos  
❯ cat hello_world.o                                 # Mirar el contenido 
❯ ld -m elf_i386 -o hello_world hello_world.o       # Creamos el binario 
❯ ./hello_world                                     # Ejecutamos el binario 
```