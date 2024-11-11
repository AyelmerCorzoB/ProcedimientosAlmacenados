# ProcedimientosAlmacenados
## OTROS EJEMPLOS

1. Escribe un procedimiento almacenado llamado insertar_producto que permita insertar un nuevo producto en la tabla productos. El procedimiento debe recibir los siguientes parámetros:

- producto (nombre del producto, tipo VARCHAR(40))
- cantidad (cantidad de productos, tipo INT)
- precio (precio del producto, tipo DECIMAL(19,2))
- marca (marca del producto, tipo VARCHAR(20))
- estado (estado del producto, tipo TINYINT)

El procedimiento debe insertar un nuevo registro en la tabla productos con los valores proporcionados, generando automáticamente el id del producto.

```sql
DELIMITER $$
CREATE PROCEDURE insertar_producto(
	IN producto VARCHAR(40),
	IN cantidad INT,
	IN precio DECIMAL(19,2),
	IN marca VARCHAR(20),
	IN estado TINYINT)
BEGIN
	INSERT INTO productos(producto,cantidad,precio,marca,estado)
 	VALUES (producto,cantidad,precio,marca,estado);
END $$
DELIMITER ;

CALL insertar_producto3('manzana',2,10,'dubandri',1);
```

2. Escribe un procedimiento almacenado llamado buscar_producto que permita consultar la información de un producto específico en la tabla productos. Este procedimiento debe recibir como parámetro el id del producto (tipo INT).

El procedimiento debe devolver el id del producto, el nombre del producto, y el subtotal calculado como el producto de cantidad y precio del producto seleccionado.
La búsqueda debe realizarse en función del id proporcionado.

```sql
DELIMITER $$

CREATE PROCEDURE buscar_producto(
    IN producto_id INT)
BEGIN
    SELECT marca FROM productos WHERE id = producto_id;
END $$
DELIMITER ;

CALL buscar_producto(7);
```

3.Escribe un procedimiento almacenado llamado insertar_producto_verify que permita insertar un nuevo producto en la tabla productos. El procedimiento debe recibir los siguientes parámetros:

- producto (nombre del producto, tipo VARCHAR(40))
- cantidad (cantidad de productos, tipo INT)
- precio (precio del producto, tipo DECIMAL(19,2))
- marca (marca del producto, tipo VARCHAR(20))
- estado (estado del producto, tipo TINYINT)
- El procedimiento debe intentar insertar un nuevo registro en la tabla productos con los valores proporcionados. Luego, verificar si la operación fue exitosa usando ROW_COUNT().

Si la inserción fue exitosa, debe devolver el mensaje: 'El registro se ha creado correctamente.'
Si la inserción falla, debe devolver el mensaje: 'Error al crear el registro.

```sql
DELIMITER $$
CREATE PROCEDURE insertar_producto_verify(
	IN producto VARCHAR(40),
	IN cantidad INT,
	IN precio DECIMAL(19,2),
	IN marca VARCHAR(20),
	IN estado TINYINT)
BEGIN
	DECLARE mensaje VARCHAR(100);
	INSERT INTO productos(producto,cantidad,precio,marca,estado)
 	VALUES (producto,cantidad,precio,marca,estado);

	IF ROW_COUNT() > 0 THEN
        SET mensaje = ('El registro se ha creado correctamente.');
    	ELSE
        SET mensaje = 'Error al crear el registro.';
    	END IF;
    	SELECT mensaje AS 'Mensaje';
END $$
DELIMITER ;

CALL insertar_producto_verify('zapatos',1,20,'nike',1);
```

## TALLER MENSAJERIA

### CREACION DE TABLAS E INSERCIONES

```SQL
CREATE DATABASE IF NOT EXISTS mensajeria;
USE mensajeria;

CREATE TABLE usuarios (
    usuario_id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    contrasena VARCHAR(100) NOT NULL,
    fecha_creacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE conversaciones (
    conversacion_id INT AUTO_INCREMENT PRIMARY KEY,
    nombre_conversacion VARCHAR(100) NULL,
    fecha_creacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE participantes (
    participante_id INT AUTO_INCREMENT PRIMARY KEY,
    conversacion_id INT NOT NULL,
    usuario_id INT NOT NULL,
    fecha_union TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (conversacion_id) REFERENCES conversaciones(conversacion_id) ON DELETE CASCADE,
    FOREIGN KEY (usuario_id) REFERENCES usuarios(usuario_id) ON DELETE CASCADE
);

CREATE TABLE mensajes (
    mensaje_id INT AUTO_INCREMENT PRIMARY KEY,
    conversacion_id INT NOT NULL,
    remitente_id INT NOT NULL,
    contenido TEXT NOT NULL,
    fecha_envio TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (conversacion_id) REFERENCES conversaciones(conversacion_id) ON DELETE CASCADE,
    FOREIGN KEY (remitente_id) REFERENCES usuarios(usuario_id) ON DELETE CASCADE
);


INSERT INTO usuarios (nombre, email, contrasena)
VALUES ('Juan Pérez', 'juan.perez@example.com', 'password123'),
('María López', 'maria.lopez@example.com', 'maria1234'),
('Carlos Martínez', 'carlos.martinez@example.com', 'carlo_pass'),
('Ana Fernández', 'ana.fernandez@example.com', 'ana2023'),
('Luis Gómez', 'luis.gomez@example.com', 'luis_pass'),
('Lucía Rivera', 'lucia.rivera@example.com', 'lucia2023'),
('Pedro Ortiz', 'pedro.ortiz@example.com', 'pedro_pass'),
('Sofía Blanco', 'sofia.blanco@example.com', 'sofiablanco123'),
('Miguel Rojas', 'miguel.rojas@example.com', 'miguerojas2023'),
('Carla Ruiz', 'carla.ruiz@example.com', 'carlitaaruiz');
```

#### Consultas

1. Cree un procedimiento almacenado que permita insertar un nuevo usuario retornando un mensaje que indique si la inserción fue satisfactoria.

```sql
DELIMITER $$
CREATE PROCEDURE insertar_usuario(
    IN nombre VARCHAR(40),
    IN email VARCHAR(100),
    IN contrasena VARCHAR(100))
    BEGIN
    DECLARE mensaje VARCHAR(100);
    INSERT INTO usuarios(nombre,email,contrasena)
    VALUES (nombre,email,contrasena);
    IF ROW_COUNT() > 0 THEN
    SET mensaje = ('El usuario se ha creado correctamente.');
    ELSE
    SET mensaje = 'Error al crear el usuario.';
    END IF;
    SELECT mensaje AS 'Mensaje';
    END $$
DELIMITER ;

CALL insertar_usuario('Ayelmer','alancorzo1512@gmail.com','3134277875');
    +----------------------------------------+
    | Mensaje                                |
    +----------------------------------------+
    | El usuario se ha creado correctamente. |
    +----------------------------------------+
```

2. Cree un procedimiento almacenado que permita eliminar un usuario retornando un mensaje que indique si la inserción fue satisfactoria.

```sql
DELIMITER $$

CREATE PROCEDURE eliminar_usuario(
    IN id INT)
BEGIN
    DECLARE mensaje VARCHAR(100);
    DELETE FROM usuarios WHERE usuario_id = id;
    IF ROW_COUNT() > 0 THEN
        SET mensaje = CONCAT('El Usuario con ID ', id, ' ha sido eliminado exitosamente.');
    ELSE
        SET mensaje = 'No se encontró ningún usuario con el ID proporcionado.';
    END IF;
    SELECT mensaje AS 'Mensaje';
END $$

DELIMITER ;

CALL eliminar_usuario(10);
+------------------------------------------------------+
| Mensaje                                              |
+------------------------------------------------------+
| El Usuario con ID 10 ha sido eliminado exitosamente. |
+------------------------------------------------------+
```

3. Cree un procedimiento almacenado que permita editar un usuario retornando un mensaje que indique si la inserción fue satisfactoria.

```sql
DELIMITER $$

CREATE PROCEDURE editar_usuario(
    IN id INT,
    IN nombreNuevo VARCHAR(100),
    IN emailNuevo VARCHAR(100),
    IN contrasenaNuevo VARCHAR(100))
BEGIN
    DECLARE mensaje VARCHAR(100);
    UPDATE usuarios
    SET nombre = nombreNuevo,
        email = emailNuevo,
        contrasena = contrasenaNuevo
    WHERE usuario_id = id;

    IF ROW_COUNT() > 0 THEN
        SET mensaje = 'El usuario se ha editado correctamente.';
    ELSE
        SET mensaje = 'Error al editar el usuario.';
    END IF;

    SELECT mensaje AS 'Mensaje';
END $$

DELIMITER ;

```

4. Cree un procedimiento almacenado que permita buscar un usuario por su nombre.

```sql
DELIMITER $$
CREATE PROCEDURE buscar_usuario(
    IN idBuscar INT
)
BEGIN
    SELECT usuario_id,
           nombre,
           email,
           contrasena,
           fecha_creacion
    FROM usuarios
    WHERE usuario_id = idBuscar;
END $$

DELIMITER ;

CALL buscar_usuario(9);

```

5. Realice una consulta que permita iniciar una conversación.

```sql
DELIMITER $$

CREATE PROCEDURE iniciar_conversacion(
    IN nombreConversacion VARCHAR(100),
    IN participante_id INT
)
BEGIN
    DECLARE conversacion_id INT;

    -- Insertar la nueva conversación
    INSERT INTO conversaciones (nombre_conversacion)
    VALUES (nombreConversacion);

    -- Obtener el ID de la conversación recién creada
    SET conversacion_id = LAST_INSERT_ID();

    -- Insertar el participante en la conversación
    INSERT INTO participantes (conversacion_id, usuario_id)
    VALUES (conversacion_id, participante_id);

    -- Devolver el ID de la conversación iniciada
    SELECT conversacion_id AS "ID de la Conversación Iniciada";
END $$

DELIMITER ;

CALL iniciar_conversacion('Conversación Inicial', 9);
+--------------------------------+
| ID de la Conversación Iniciada |
+--------------------------------+
|                              3 |
+--------------------------------+

```

6. Realice un procedimiento almacenado que permita agregar un nuevo participante a la conversación y valide si hay capacidad disponible.
   La cantidad maxima por cada conversación son 5 usuarios.

```sql
DELIMITER $$

CREATE PROCEDURE agregar_participante(
    IN conversacion_id INT,
    IN usuario_id INT
)
BEGIN
    DECLARE num_participantes INT;
    DECLARE mensaje VARCHAR(100);

    -- Contar el número actual de participantes en la conversación
    SELECT COUNT(*) INTO num_participantes
    FROM participantes
    WHERE conversacion_id = conversacion_id;

    -- Verificar si hay capacidad para agregar más participantes
    IF num_participantes < 5 THEN
        -- Insertar al nuevo participante en la conversación
        INSERT INTO participantes (conversacion_id, usuario_id)
        VALUES (conversacion_id, usuario_id);
        SET mensaje = 'Participante agregado exitosamente.';
    ELSE
        -- Si la conversación ya tiene 5 participantes, no se agrega más
        SET mensaje = 'La conversación ya tiene el número máximo de participantes.';
    END IF;

    -- Devolver el mensaje de éxito o error
    SELECT mensaje AS 'Mensaje';
END $$

DELIMITER ;
CALL agregar_participante(1, 22);

```

7. Realice un procedimiento que permita visualizar los mensaje de una conversación.

8. Realice un procedimiento almacenado para enviar un mensaje a una conversación.

Modifica la estructura de la tabla para que permita el envio de los mensaje a todos los usuarios o a un usuario en particular. y realice las siguientes consultas.

9. Realice un procedimiento almacenado que permita visualizar los mensajes de un usuario especifico.

10. Realice un procedimiento almacenado que permita eliminar a un usuario que no esta respetando las normas de buena conversación. Agregue una tabla que permita agregar palabra no deseadas. El procedimiento debe inspeccionar la conversación a verificar.
