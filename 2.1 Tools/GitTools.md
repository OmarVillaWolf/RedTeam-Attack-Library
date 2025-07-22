# GitTools

```bash
❯ git clone https://github.com/internetwache/GitTools.git # Descargar las herramientas de GitTools y poder usar Dumper, Extractor o Finder

	# Dumper nos ayudara a extraer los directorios, datos que se encuentran dentro del Git
	# Extractor nos ayudara a encontrar los directorios que estan incompletos 
```

```bash
❯ ./gitdumper.sh ❮http://IP/.git/❯ Website/

	# gitdumper = Ejecutar el comando
	# IP = Dominio a escanear 
	# .git = Queremos que todo lo que encuentre de git lo extraiga 
	# Website = Que todo lo que encuentre lo guarde en ese archivo llamado Website 
```

```bash
❯ ./extractor.sh ../Dumper/Website ./website

	# extractor -> Ejecutar el comando
	# Dumper/Website -> Path de el comando anterior y el nombre del archivo que le habiamos puesto
	# website -> Que lo que extraiga nos lo guarde en el archivo llamado website
```

