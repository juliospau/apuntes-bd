# DDL (Data Definition Language)

**Este sublenguaje de SQL nos permite definir las características u objetos de una BBDD. Sus principales cláusulas son:**

Cláusula | Acción
--- | ---
**`CREATE`** | Nos permite crear una BBDD, una tabla para una BBDD existente, un usuario (en DCL) o un esquema.
**`ALTER`** | Nos permite añadir, eliminar o modificar columnas de una tabla y también añadir o eliminar restricciones.
**`DROP`** | Elimina una tabla de una BBDD.
**`TRUNCATE`** | Elimina los datos de una tabla, pero no la tabla en sí.
**`RENAME`** | Nos permite renombrar una tabla.

* [Más información sobre DDL en MariaDB](https://mariadb.com/kb/en/data-definition/)

## **`CREATE DATABASE | SCHEMA`**

### Fórmula `CREATE DATABASE | SCHEMA`

```SQL
CREATE (DATABASE | SCHEMA) [IF-NOT-EXISTS] nombreDB [CHARACTER SET = NOMBRE_CHARSET] [COLLATE = NOMBRE_REGLAS_COMPARACIÓN];

-- En MariaDB existe la cláusula [OR REPLACE], que creará la BBDD si no existe y si existe eliminará la que está creada para crear una nueva.
```

Ejemplo en MariaDB:

```SQL
CREATE DATABASE My_Database CHARACTER SET = utf8 COLLATE = utf8_general_ci;
```

- Crea la BBDD 'My_Database' siempre y cuando no exista.
- Con **`CHARACTER SET = ...`** le indicamos la codificación de caracteres que vamos a utilizar. **utf8mb4** es la implementación de
UTF-8 utilizada en MariaDB. Luego le indicamos qué reglas queremos utilizar para comparar los caracteres unos de otros con **`COLLATE`**.
**utf8_spanish_ci** es el utilizado para comparar caracteres en castellano.
- **[Creación de BBDD en MariaDB](https://mariadb.com/kb/en/create-database/)**
- **[Codificaciones implementadas en MariaDB](https://mariadb.com/kb/en/supported-character-sets-and-collations/)**
- **[Relación CHARSET-COLLATE](https://stackoverflow.com/questions/341273/what-does-character-set-and-collation-mean-exactly#341333)**

Ejemplo en PostgreSQL:

```SQL
CREATE DATABASE music2
    LC_COLLATE 'sv_SE.iso885915' LC_CTYPE 'sv_SE.iso885915' -- LC_CTYPE indica la clasificación de los caracteres utilizada en la BBDD
    ENCODING LATIN9
    TEMPLATE template0; -- Esqueleto de la BBDD utilizado en PostgreSQL
```

- **[Declaración de la codificación en PostgreSQL](https://www.postgresql.org/docs/12/sql-createdatabase.html)**
- **[Codificación en PostgreSQL](https://www.postgresql.org/docs/12/multibyte.html#MULTIBYTE-CHARSET-SUPPORTED)**
- **[Documentación de PostgreSQL](https://www.postgresql.org/files/documentation/pdf/12/postgresql-12-US.pdf)**

---

## **`CREATE TABLE`**

### Fórmula `CREATE TABLE`

```SQL
CREATE TABLE [IF-NOT-EXISTS] Nombre_Tabla (
<atributo1> [DOMINIO] [PRIMARY KEY],
<atributo2> [DOMINIO] [NOT NULL] [UNIQUE] ...,
<atributon> [DOMINIO] [NOT NULL] [UNIQUE] ...,
[CONSTRAINT ...] -- Restricciones
);
```

- **Para crear tablas empleamos la cláusula compuesta `CREATE TABLE`, seguida del nombre de la tabla.**
- **Una tabla se compone de columnas (atributos) y tuplas de información relacionada con los atributos. Para diferenciar las tuplas unas de otras**
**empleamos las claves primarias y éstas las podemos declarar con el identificador `PRIMARY KEY` después de la declaración del dominio o después de la declaración de atributos, a modo de restricción.**
- **Podemos emplear `NOT NULL` para indicar que no se permite la introducción de valores nulos en una columna en concreto.**
- **Empleamos `UNIQUE` para indicar que no queremos que se repitan los valores de una columna.**
- **Separamos los distintos atributos mediante comas.**
- **El bloque de código empleado para crear la tabla siempre tiene que acabar en ;.**

Ejemplo:

```SQL
CREATE TABLE Alumno (
    id INTEGER PRIMARY KEY, -- `PRIMARY KEY` implica `NOT NULL` y `UNIQUE`
    nombre VARCHAR(20) NOT NULL,    -- nombre tendrá un máximo de 20 caracteres y no podrá contener un valor nulo
    apellidos VARCHAR(50),  -- apellidos tendrá un máximo de 50 caracteres
    fecha_nacimiento DATE -- fecha_nacimiento será un dato de tipo fecha, cumpliendo el formato YYYY-MM-DD
);
```

---

## **`CREATE DOMAIN`**

**Los dominios indican el tipo de datos que queremos emplear para un atributo. El tipo de datos condicionará los posibles valores que una columna podrá tomar.**

### Fórmula `CREATE DOMAIN`

**La manera más eficiente de controlar los dominios en las BBDD es declarándolos por adelantado y luego llamándolos desde las tablas. Ésto lo hacemos empleando la siguiente fórmula:**

```SQL
CREATE DOMAIN [NOMBRE_DOMINIO] [DOMINIO];
```

---

:warning: **Ni MySQL ni MariaDB tienen una implementación de `CREATE DOMAIN`. Ésta clásula solo la encontramos, por ejemplo, en PostgreSQL.**

- **[Cuestión en StackOverflow](https://dba.stackexchange.com/questions/121471/creating-a-domain-in-mysql-or-mariadb)**

- **[Petición de la implementación aún sin resolver](https://jira.mariadb.org/browse/MDEV-16377)**

- **[CREATE DOMAIN en PostgreSQL](https://www.postgresql.org/docs/current/sql-createdomain.html)**

---

Ejemplo:

```SQL
CREATE DOMAIN Tipo_Entero INT(4);
CREATE DOMAIN Nombre_Valido VARCHAR(20);
CREATE DOMAIN Apellido_Valido VARCHAR(50);

CREATE TABLE Alumno (
    id Tipo_Entero PRIMARY KEY, -- id tendrá el dominio Tipo_Entero, que declaramos antes y que consiste en un entero con un máximo de 4 dígitos
    nombre Nombre_Valido NOT NULL,    -- nombre será del tipo Nombre_Valido
    apellidos Apellido_Valido,  -- apellidos será del tipo Apellido_Valido
    fecha_nacimiento DATE -- fecha_nacimiento seguirá siendo un dato de tipo fecha, cumpliendo el formato YYYY-MM-DD
);
```

**Algunos de los tipos de datos existentes en MariaDB son los siguientes:**

NUMÉRICOS |
--------- |
**INT(M):** (INTEGER) número entero. Cuando está asignado va desde -2147483648 hasta 2147483647. Cuando no lo está va desde 0 hasta 4294967295. https://mariadb.com/kb/en/int/|
**BOOLEAN:** valores booleanos (verdadero o falso - true or false). 0 se considera falso y cualquier otro valor es verdadero. https://mariadb.com/kb/en/boolean/|
**BIGINT(M):** número entero largo. Cuando está asignado va desde -9223372036854775808 hasta 9223372036854775807. Cuando no lo está va desde 0 hasta 18446744073709551615. https://mariadb.com/kb/en/bigint/|
**DECIMAL(M[, D]):** valor decimal. Permite como máximo 65 dígitos en la parte entera y 38 en la mantisa. Si se especifica UNSIGNED no se permitirán los valores negativos. https://mariadb.com/kb/en/decimal/|
**FLOAT(M,D):** valores de coma flotante. Sus valores pueden ser desde -3.402823466E+38 hasta -1.175494351E-38, 0 y desde 1.175494351E-38 hasta 3.402823466E+38. https://mariadb.com/kb/en/float/|
**DOUBLE(M,D):** (valores de coma flotante de doble precisión. Sus valores pueden ser desde -1.7976931348623157E+308 hasta -2.2250738585072014E-308, 0 y desde 2.2250738585072014E-308 hasta 1.7976931348623157E+308. https://mariadb.com/kb/en/double/|

---

 CADENAS-CARACTERES|
 ------------------|
**STRING:** cadenas de caracteres. Pueden ir entre comillas simples o dobles y permiten las [secuencias de escape](./img/secuencias_escape.png). https://mariadb.com/kb/en/string-literals/|
**CHAR(M):** cadenas de caracteres de longitud fija: 0 a 255 caracteres. https://mariadb.com/kb/en/char/|
**VARCHAR(M):** cadenas de longitud variable. Permite cadenas desde 0 a 65532 caracteres. https://mariadb.com/kb/en/varchar/|
**MEDIUMTEXT:** cadenas de hasta 16,777,215 caracteres. https://mariadb.com/kb/en/mediumtext/|
**LONGTEXT:** cadenas de hasta 4,294,967,295 caracteres. https://mariadb.com/kb/en/longtext/|

---

FECHA-HORA|
----------|
**DATE:** fechas. Los valores siguen el formato "YYYY-MM-DD". https://mariadb.com/kb/en/date/|
**TIME:** horas, minutos y segundos. Permite especificar la hora siguiendo el formato "HH:MM:SS.ssssss". https://mariadb.com/kb/en/time/|
**DATETIME:** unión de los formatos para fecha y hora. Sigue el formato "YYYY-MM-DD HH:MM:SS.ffffff". https://mariadb.com/kb/en/datetime/|
**TIMESTAMP:** igual que DATETIME. Se suele utilizar para guardar un registro de las inserciones y actualizaciones de datos. https://mariadb.com/kb/en/timestamp/|

---

OTROS|
-----|
**NULL:** valor desconocido. No es un valor cero o vacío.|
**AUTO_INCREMENT:** cada vez que se introducen nuevos datos y al tener este dominio una columna en concreto, sus valores irán en aumento. Suele utilizarse para claves primarias. https://mariadb.com/kb/en/auto_increment/|

- **Hay que tener en cuenta que los tipos de datos pueden variar según el sistema gestor que empleemos.**
- **[Más información sobre tipos de datos](https://www.tutorialcup.com/dbms/datatypes-variables.htm)**
- **[Más información sobre tipos de datos en MariaDB](https://mariadb.com/kb/en/data-types/)**

---

### RESTRICCIONES

El modelo relacional está compuesto de restricciones, reglas que impiden la entrada de datos que no cumplan unos requisitos que les imponemos. Existen reglas inherentes al modelo (regla de integridad referencial, regla de las entidades...) y reglas semánticas:

- **Unicidad:** indica que los valores de un atributo no se pueden repetir. **`UNIQUE`**
- **Obligatoriedad:** indica que un atributo no admite valores nulos. **`NOT NULL`**
- **Verificación:** a la hora de hacer una operación de actualización comprueba si se cumplen las condiciones de un predicado en concreto, y si no se cumplen se rechaza la operación. **`CHECK`**
- **Clave ajena:** esta es una restricción inherente al modelo relacional que definirá las reglas a seguir al refenciar las claves entre las relaciones de entidades: si una entidad E2 tiene un atributo que es una clave primaria de una entidad E1, el valor de dicho atributo debe concordar con el valor de la clave primaria referenciada de E1 o ser nulo.

A la hora de definir una clave ajena debemos tener en cuenta las consecuencias de las operaciones (borrado y modificación) sobre la tupla de la entidad que contiene la clave que referenciamos.
Podemos definir las acciones que se llevarán a cabo en caso de que, por ejemplo, se elimine la tupla en concreto que contiene una clave que estamos referenciando desde una entidad E2.
Estas operaciones son las siguientes:

- **`NO ACTION`:** el borrado (o la modificación) de una tupla que contiene una clave primaria que estamos referenciando desde otra/s entidad/es no provocará la eliminación de datos en la/s entidad/es que referencia/n.
- **`CASCADE`:** el borrado (o la modificación) de una tupla que contiene una clave primaria que estamos referenciando desde otra/s entidad/es provocará el borrado en cascada de las tuplas que contienen la clave referenciada.
- **`SET NULL`:** el borrado (o la modificación) de una tupla que contiene una clave primaria que estamos referenciando desde otra/s entidad/es provocará el cambio a valores nulos de todas las tuplas que contienen la clave ajena.
- **`SET DEFAULT`:** el borrado (o la modificación) de una tupla que contiene una clave primaria que estamos referenciando desde otra/s entidad/es provocará el cambio a valores por defecto (que previamente hemos definido) en todas las tuplas que contienen la clave ajena.

Ejemplos:

```SQL
CREATE DOMAIN Tipo_Entero INT(4);
CREATE DOMAIN Nombre_Valido VARCHAR(20);
CREATE DOMAIN Apellido_Valido VARCHAR(50);

CREATE TABLE Alumno (
    id Tipo_Entero PRIMARY KEY,
    nombre Nombre_Valido NOT NULL,
    apellidos Apellido_Valido,
    fecha_nacimiento DATE
);

CREATE TABLE Profesor (
    id Tipo_Entero,
    nombre Nombre_Valido NOT NULL,
    apellidos Apellido_Valido,
    fecha_nacimiento DATE,
    id_alumno Tipo_Entero,  -- Declaramos el atributo id_alumno, que a modo de clave ajena referenciará la clave primaria de cada tupla de la tabla Alumno.
    PRIMARY KEY (id),       -- Como podemos ver, la restricción de clave primaria se puede poner después de la declaración del atributo que contendrá a éstas claves.
    UNIQUE (nombre),        -- Lo mismo pasa con UNIQUE, y cualquier otra restricción.
    FOREIGN KEY (id_alumno) REFERENCES Alumno (id)  -- Indicamos que id_alumno, atributo previamente declarado, referencia a la clave primaria 'id' de la tabla 'Alumno'
    ON UPDATE CASCADE   -- Indicamos que en caso de modificar la tupla que contiene la clave primaria de un alumno que referenciamos desde la tabla Profesor a modo de clave ajena, los registros también se actualicen en la tabla Profesor. 
    ON DELETE NO ACTION -- En caso de eliminación en la tabla Alumnos, no eliminar la tupla de la tabla profesor que contiene la clave ajena.
/*
También podemos utilizar la palabra clave CONSTRAINT para crear una restricción con un nombre en concreto:

CONSTRAINT <nombre_restricción> PRIMARY KEY (<atributon>) => CONSTRAINT Clave_Primaria PRIMARY KEY (id)
                                                          => CONSTRAINT Restricción_Unicidad_Nombre UNIQUE (nome)

* El nombramiento de una restricción conllevará una mayor claridad en el código.
* Hay que tener en cuenta que a la hora de referenciar una clave ajena lo debemos hacer empleando el mismo dominio de la clave primaria que referenciamos. En este caso se crea el atributo id_alumno, que referencia a id de la tabla Alumno y que es de tipo Tipo_Entero, al igual que id.

*/
);
```

```SQL
CREATE DOMAIN Tipo_Entero INT(4);
CREATE DOMAIN Nombre_Valido VARCHAR(20);
CREATE DOMAIN Apellido_Valido VARCHAR(50);

CREATE TABLE Alumno (
    id Tipo_Entero PRIMARY KEY,
    nombre Nombre_Valido NOT NULL,
    apellidos Apellido_Valido,
    fecha_nacimiento DATE,
    edad INTEGER NOT NULL,
    CONSTRAINT Comprobar_Edad_Válida CHECK (edad BETWEEN 13 AND 18) -- Comprobamos que la edad del alumno esté comprendida entre 13 y 18 años mediante el predicado como argumento de la restricción de validación - CHECK. Si no se cumple (false), no se permitirá la introducción de la edad.

/*

CHECK permite comprobar que los datos que introducimos en una columna en concreto cumplen una condiciones que le imponemos mediante predicados.

CONSTRAINT <Nombre_Restricción> CHECK (<predicado>) [NOT DEFERRABLE | DEFERRABLE] [INITIALLY IMMEDIATE | INITIALLY DEFERRED]

DEFERRABLE + INITIALLY IMMEDIATE: todas las tuplas son comprobadas al final de la inserción/actualización. 
DEFERRABLE + INITIALLY DEFERRED: todas las tuplas son comprobadas al final de la transacción.
NOT DEFERRABLE: cada tupla es comprobada en el momento de la inserción/actualización.

MÁS INFORMACIÓN SOBRE (NOT)DEFERRABLE: https://stackoverflow.com/questions/5300307/not-deferrable-versus-deferrable-initially-immediate#5300418
*/
);
```

---

## `ALTER`

**`ALTER`** nos permite alterar las tablas y sus restricciones. Podemos añadir **(ADD)** o eliminar **(DROP)** columnas o restricciones.

### Fórmula de `ALTER`

```SQL
ALTER TABLE <Nombre_Tabla> ADD COLUMN <atributo1> <dominio1> [NOT NULL] [DEFAULT <x>]
                           DROP COLUMN <atributo1> [CASCADE | RESTRICT]
                           ADD <restricción>
                           DROP <restricción>
                           MODIFY COLUMN <dominio1>;

/*
- DROP [column] <atributon> CASCADE -> Borra la columna que indicamos y todas las demás que hacen referencia a algún atributo suyo.
- DROP [column] <atributon> RESTRICT -> Borra la columna que indicamos sin afectar a las demás que hacen referencia a algún atributo suyo.
*/
```

Ejemplo:

```SQL
CREATE DOMAIN Tipo_Entero INT(4);
CREATE DOMAIN Nombre_Valido VARCHAR(20);
CREATE DOMAIN Apellido_Valido VARCHAR(50);

CREATE TABLE Alumno (
    id Tipo_Entero PRIMARY KEY,
    nombre Nombre_Valido NOT NULL,
    apellidos Apellido_Valido,
    fecha_nacimiento DATE,
    edad INTEGER NOT NULL
);

ALTER TABLE Alumno ADD Curso_Actual Nombre_Valido NOT NULL; -- Añade el atributo Curso_Actual, con el dominio Nombre_Valido, sin valores nulos y ésto a la tabla Alumno.
ALTER TABLE Alumno ADD CONSTRAINT Comprobar_Edad_Válida CHECK (edad BETWEEN 13 AND 18); -- Añade la restricción 'Comprobar_Edad_Válida', que comprobará si la edad introducida está comprendida entre 13 y 18.
ALTER TABLE Alumno ADD CONSTRAINT Comprobar_Curso_Válido CHECK (Curso_Actual IN ('ASIR1', 'DAM1', 'SMR1')); -- Añade la restricción Comprobar_Curso_Válido, que comprobará si el curso actual del alumno está entre la lista dada ('ASIR1', 'DAM1' o 'SMR1').
ALTER TABLE Alumno DROP COLUMN edad; -- Elimina la columna edad.
```

## `DROP`

**`DROP`** nos permite eliminar una tabla o una base de datos al completo.

### Fórmula de `DROP`:

```SQL
DROP TABLE [IF EXISTS] <Nombre_Tabla> [CASCADE | RESTRICT];
DROP (DATABASE | SCHEMA) [IF EXISTS] <Nombre_BBDD> [CASCADE | RESTRICT];

-- RESTRICT comprueba antes del borrado si la tabla o la BBDD está vacía.
-- CASCADE lo elimina todo, sin contemplaciones.
```

Ejemplo:

```SQL
DROP TABLE Alumno CASCADE; -- Elimina toda la tabla Alumno.
```

## `TRUNCATE`

**`TRUNCATE`** nos sirve para eliminar el contenido de la tabla, pero no su estructura.

### Fórmula de `TRUNCATE`:

```SQL
TRUNCATE TABLE Nombre_Tabla;

-- En MariaDB TRUNCATE elimina el contenido y recrea la tabla, lo que es más rápido que DELETE (clásula de DML), que elimina una a una las tuplas de la tabla.

```

## `RENAME`

**`RENAME`** nos sirve para renombrar una tabla.

### Fórmula de `RENAME`:

```SQL
RENAME TABLE Nombre_Tabla TO Nuevo_Nombre_Tabla [, Nombre_Tabla_2 TO Nuevo_Nombre_Tabla_2];
```

Ejemplo:

```SQL
RENAME TABLE Profesor TO Docente;
```
