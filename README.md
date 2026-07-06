# BD
/* =====================================================
   DDL (Data Definition Language)
   Sirve para definir y modificar la estructura
   de la base de datos y sus tablas.
   ===================================================== */

/* Crear una base de datos */
CREATE DATABASE empresa_db;

/* Seleccionar la base de datos */
USE empresa_db;

/* Crear una tabla */
CREATE TABLE empleados (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    cargo VARCHAR(50),
    salario DECIMAL(10,2)
);

/* Agregar una nueva columna */
ALTER TABLE empleados
ADD fecha_ingreso DATE;

/* Renombrar una tabla */
RENAME TABLE empleados TO trabajadores;

/* =====================================================
   DML (Data Manipulation Language)
   Sirve para insertar, modificar y eliminar datos.
   ===================================================== */

/* Insertar registros */
INSERT INTO trabajadores
(nombre, cargo, salario, fecha_ingreso)
VALUES
('Juan Pérez', 'Desarrollador', 3500.00, '2025-01-10'),
('Ana Gómez', 'Diseñadora', 3200.00, '2025-02-15');

/* Actualizar un registro */
UPDATE trabajadores
SET salario = 4000.00
WHERE id = 1;

/* Eliminar un registro */
DELETE FROM trabajadores
WHERE id = 2;

/* =====================================================
   DQL (Data Query Language)
   Sirve para consultar información.
   ===================================================== */

/* Consultar todos los registros */
SELECT * FROM trabajadores;

/* Consultar columnas específicas */
SELECT nombre, salario
FROM trabajadores;

/* Consultar con condición */
SELECT *
FROM trabajadores
WHERE salario > 3500;

/* =====================================================
   TCL (Transaction Control Language)
   Sirve para controlar transacciones.
   ===================================================== */

/* Desactivar autocommit */
SET autocommit = 0;

/* Iniciar transacción */
START TRANSACTION;

/* Realizar una modificación */
UPDATE trabajadores
SET salario = 4500
WHERE id = 1;

/* Confirmar cambios */
COMMIT;

/* Otra transacción */
START TRANSACTION;

/* Modificación temporal */
UPDATE trabajadores
SET salario = 5000
WHERE id = 1;

/* Deshacer cambios */
ROLLBACK;

/* =====================================================
   DCL (Data Control Language)
   Sirve para administrar permisos y seguridad.
   ===================================================== */

/* Crear usuario */
CREATE USER 'practicante'@'localhost'
IDENTIFIED BY 'Clave123';

/* Otorgar permisos */
GRANT SELECT, INSERT
ON empresa_db.*
TO 'practicante'@'localhost';

/* Aplicar cambios de privilegios */
FLUSH PRIVILEGES;

/* Revocar permisos */
REVOKE INSERT
ON empresa_db.*
FROM 'practicante'@'localhost';

/* =====================================================
   DDL (eliminación)
   ===================================================== */

/* Eliminar tabla */
DROP TABLE trabajadores;

/* Eliminar base de datos */
DROP DATABASE empresa_db;

| Tipo    | Significado                  | Comandos comunes                                       |
| ------- | ---------------------------- | ------------------------------------------------------ |
| **DDL** | Data Definition Language     | `CREATE`, `ALTER`, `DROP`, `TRUNCATE`, `RENAME`        |
| **DML** | Data Manipulation Language   | `INSERT`, `UPDATE`, `DELETE`                           |
| **DQL** | Data Query Language          | `SELECT`                                               |
| **TCL** | Transaction Control Language | `COMMIT`, `ROLLBACK`, `SAVEPOINT`, `START TRANSACTION` |
| **DCL** | Data Control Language        | `GRANT`, `REVOKE`                                      |


Diagnóstico Inicial

La tabla Dataset_RiwiSupply contiene 90 registros y 27 columnas en una única hoja desnormalizada.

Se identificaron los siguientes problemas:

Problema	Ejemplo Detectado
Proveedores duplicados	"Ferreteria Torres S.A.S." / "FERRETERIA TORRES SAS"
Ciudades inconsistentes	Bogotá / Bogota / BOGOTÁ / bogota, d.c.
Países inconsistentes	Colombia / CO / Col / COLOMBIA
Fechas con formatos mixtos	2023-07-12 / 25-08-2023
Tipos de movimiento inconsistentes	Entrada / ingreso / ENTRADA
Unidades de medida inconsistentes	UND / Unidad / und
Productos escritos de distintas formas	INTERRUPTOR SENCILLO / interruptor simple
Teléfonos inconsistentes	3101234567 / 310-123-4567
Valores nulos	precio_unitario, email_proveedor, nit_proveedor
Totales incorrectos	valor_total_compra ≠ precio_unitario × cantidad
Filas duplicadas	Registros repetidos
Filas casi vacías	Registros 87–90
Dependencias transitivas	Ciudad y País almacenados junto con proveedor
2. Fase de Limpieza de Datos
Regla 1: Estandarización de Fechas

Convertir todas las fechas al estándar ISO:

YYYY-MM-DD

Ejemplos:

Original	Corregido
25-08-2023	2023-08-25
12/07/2023	2023-07-12
2023-07-12	2023-07-12
Regla 2: Estandarización de Proveedores

Convertir a formato título.

Ejemplos:

Original	Corregido
TECNOPARTES LTDA	Tecnopartes Ltda
Tecno Partes Ltda.	Tecnopartes Ltda
tecnopartes ltda	Tecnopartes Ltda
Regla 3: Estandarización de Ciudades
Original	Corregido
BOGOTÁ	Bogotá
Bogota	Bogotá
bogota, d.c.	Bogotá
B/quilla	Barranquilla
Regla 4: Estandarización de Países
Original	Corregido
CO	Colombia
Col	Colombia
COLOMBIA	Colombia
Regla 5: Teléfonos

Formato único:

+57XXXXXXXXXX

Ejemplo:

3101234567
↓
+573101234567
Regla 6: Correos Electrónicos

Todo en minúsculas.

Ventas@Empresa.COM
↓
ventas@empresa.com
Regla 7: Unidades de Medida
Original	Corregido
UND	Unidad
und	Unidad
Unidad	Unidad
mt	Metro
mts	Metro
ML	Metro
Regla 8: Tipos de Movimiento
Original	Corregido
ingreso	Entrada
ENTRADA	Entrada
entrada	Entrada
egreso	Salida
SALIDA	Salida
Regla 9: Totales Incorrectos

Recalcular:

valor_total_compra =
precio_unitario * cantidad_comprada
Regla 10: Eliminar Duplicados

Eliminar filas completamente repetidas.

Regla 11: Eliminar Registros Vacíos

Eliminar filas con mayoría de campos NULL.

3. Primera Forma Normal (1FN)
Objetivo

Eliminar grupos repetidos y garantizar atomicidad.

Problema detectado

La tabla mezcla:

Compras
Productos
Proveedores
Bodegas
Movimientos

en una sola estructura.

Clave candidata
(id_registro, id_producto)

porque una compra puede contener varios productos.

Resultado en 1FN

Todos los campos contienen un único valor.

Ejemplo correcto:

ID Compra	Producto
1	Tornillo
1	Tuerca
1	Arandela
4. Segunda Forma Normal (2FN)
Objetivo

Eliminar dependencias parciales.

Dependencias observadas

Con clave compuesta:

(ID_Compra, ID_Producto)

hay atributos que dependen solamente de una parte de la clave.

Ejemplos:

nombre_proveedor
↓
depende de id_proveedor
nombre_producto
↓
depende de id_producto
Tablas resultantes
PROVEEDOR
ID_Proveedor
Nombre
NIT
Teléfono
Email
Ciudad
PRODUCTO
ID_Producto
Nombre
Descripción
Categoría
Unidad
BODEGA
ID_Bodega
Nombre
Ciudad
Dirección
COMPRA
ID_Compra
Fecha
ID_Proveedor
ID_Bodega
Observaciones
DETALLE_COMPRA
ID_Compra
ID_Producto
Cantidad
Precio
5. Tercera Forma Normal (3FN)
Objetivo

Eliminar dependencias transitivas.

Problema

En proveedor:

ID_Proveedor
↓
Ciudad
↓
País

El país no depende directamente del proveedor.

Solución

Crear tabla CIUDAD.

CIUDAD
ID_Ciudad
Nombre_Ciudad
Pais
PROVEEDOR
ID_Proveedor
Nombre
NIT
Teléfono
Email
ID_Ciudad

Ahora:

ID_Proveedor
→ ID_Ciudad
→ País

queda correctamente normalizado.

6. Modelo Final en 3FN
CIUDAD
PK
ID_Ciudad
Atributos
Nombre_Ciudad
Pais
PROVEEDOR
PK
ID_Proveedor
FK
ID_Ciudad
Atributos
Nombre_Proveedor
NIT
Telefono
Email
CATEGORIA_PRODUCTO
PK
ID_Categoria
Atributos
Nombre_Categoria
PRODUCTO
PK
ID_Producto
FK
ID_Categoria
Atributos
Nombre_Producto
Descripcion
Unidad_Medida
BODEGA
PK
ID_Bodega
Atributos
Nombre_Bodega
Ciudad_Bodega
Direccion_Bodega
COMPRA
PK
ID_Compra
FK
ID_Proveedor
ID_Bodega
Atributos
Fecha_Compra
Valor_Total
Observaciones
DETALLE_COMPRA
PK Compuesta
(ID_Compra, ID_Producto)
FK
ID_Compra
ID_Producto
Atributos
Cantidad
Precio_Unitario
7. Cardinalidades
Ciudad      1 ---- N Proveedor

Proveedor   1 ---- N Compra

Bodega      1 ---- N Compra

Compra      1 ---- N Detalle_Compra

Producto    1 ---- N Detalle_Compra

Categoria   1 ---- N Producto
8. Código Mermaid.js (DER)
erDiagram

    CIUDAD {
        int ID_Ciudad PK
        string Nombre_Ciudad
        string Pais
    }

    PROVEEDOR {
        int ID_Proveedor PK
        string Nombre_Proveedor
        string NIT
        string Telefono
        string Email
        int ID_Ciudad FK
    }

    CATEGORIA_PRODUCTO {
        int ID_Categoria PK
        string Nombre_Categoria
    }

    PRODUCTO {
        int ID_Producto PK
        int ID_Categoria FK
        string Nombre_Producto
        string Descripcion
        string Unidad_Medida
    }

    BODEGA {
        int ID_Bodega PK
        string Nombre_Bodega
        string Ciudad_Bodega
        string Direccion_Bodega
    }

    COMPRA {
        int ID_Compra PK
        date Fecha_Compra
        int ID_Proveedor FK
        int ID_Bodega FK
        decimal Valor_Total
        string Observaciones
    }

    DETALLE_COMPRA {
        int ID_Compra PK, FK
        int ID_Producto PK, FK
        int Cantidad
        decimal Precio_Unitario
    }

    CIUDAD ||--o{ PROVEEDOR : posee
    PROVEEDOR ||--o{ COMPRA : realiza
    BODEGA ||--o{ COMPRA : almacena
    COMPRA ||--o{ DETALLE_COMPRA : contiene
    PRODUCTO ||--o{ DETALLE_COMPRA : participa
    CATEGORIA_PRODUCTO ||--o{ PRODUCTO : clasifica




