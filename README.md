Guía Completa: Instalar MariaDB en Docker y Solucionar Errores

Esta guía cubre:
✅ Instalación de MariaDB en Docker.
✅ Solución a errores de autenticación (401 Unauthorized).
✅ Descarga manual de la imagen y carga en Docker.
✅ Creación de una base de datos y tabla.
✅ Configuración de persistencia de datos.


---

1️⃣ Verificar que Docker esté instalado y funcionando

Antes de empezar, asegúrate de que Docker está instalado y activo.

1. Abrir Docker Desktop y verificar que esté corriendo.


2. Comprobar el estado de Docker en powesherll con:

docker info

Si ves información detallada, Docker está funcionando correctamente.

Si hay un error como "Cannot connect to the Docker daemon", asegúrate de que Docker esté en ejecución.


---

2️⃣ Iniciar sesión en Docker Hub para evitar errores (401 Unauthorized)

Desde 2021, Docker limita las descargas anónimas. Debes iniciar sesión antes de descargar imágenes.



3️⃣ Descargar la imagen de MariaDB desde Docker Hub

Ahora que iniciaste sesión correctamente, puedes descargar la imagen de MariaDB. Ejecuta el siguiente comando:

docker pull mariadb:10.6

Cuando la descarga termine, puedes verificar que la imagen esté disponible con:

docker images

Verás la imagen de mariadb listada con la versión 10.6.


---

4️⃣ Crear y ejecutar un contenedor MariaDB

Para crear y ejecutar un contenedor de MariaDB, usa este comando en PowerShell o CMD:

docker run -d \
  --name mariadb_server \
  -e MYSQL_ROOT_PASSWORD=mi_contraseña \
  -e MYSQL_DATABASE=mi_base_de_datos \
  -e MYSQL_USER=mi_usuario \
  -e MYSQL_PASSWORD=usuario_contraseña \
  -p 3307:3306 \
  mariadb:10.6

📌 Explicación:

-d: Ejecuta el contenedor en segundo plano.

--name mariadb_server: Nombre del contenedor.

-e MYSQL_ROOT_PASSWORD=mi_contraseña: Contraseña del usuario root.

-e MYSQL_DATABASE=mi_base_de_datos: Crea una base de datos automáticamente.

-p 3307:3306: Expone el puerto 3306 del contenedor en el puerto 3307 de tu máquina, para evitar conflictos si ya está en uso.


Nota: Si el nombre del contenedor ya está en uso, puedes detener y eliminar el contenedor anterior con los siguientes comandos:

1. Detener el contenedor existente:

docker stop mariadb_server


2. Eliminar el contenedor existente:

docker rm mariadb_server


Luego, vuelve a ejecutar el comando para crear el nuevo contenedor.


---

5️⃣ Verificar que MariaDB está corriendo

Ejecuta:

docker ps

Si ves mariadb_server en la lista, todo está bien.

Si no aparece, revisa los logs con:

docker logs mariadb_server


---

6️⃣ Conectar a la base de datos dentro del contenedor

Para acceder a MariaDB dentro del contenedor, usa este comando en PowerShell o CMD:


docker exec -it mariadb_server mysql -u root -p


docker exec permite ejecutar comandos dentro de un contenedor.

-it asegura que puedas interactuar con el contenedor en modo terminal.

mariadb_server es el nombre del contenedor.

mysql es el cliente de MariaDB.

-u root indica que te conectarás como usuario root (usando la contraseña que configuraste en el paso anterior).

-p te pedirá que ingreses la contraseña del usuario root.


🔹 Cuando te pida la contraseña, ingresa la contraseña que configuraste en el paso anterior con el parámetro MYSQL_ROOT_PASSWORD la cual es : mi_contraseña


---

7️⃣ Crear una tabla en la base de datos

Ahora que estás dentro de MariaDB, puedes ejecutar comandos SQL en el mismo poweshell para interactuar con tu base de datos.

1. Seleccionar la base de datos:

USE mi_base_de_datos;


2. Crear una tabla usuarios:

CREATE TABLE usuarios (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    fecha_registro TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);


3. Verificar la tabla creada:

SHOW TABLES;


4. Consultar la estructura de la tabla:

DESCRIBE usuarios;


5. Salir de MariaDB:

EXIT;



---

8️⃣ Configurar persistencia de datos (en caso de que no quieras perder datos si se elimina un contenedor usa volúmenes persistentes)



1️⃣ Detener y eliminar el contenedor actual:

docker stop mariadb_server
docker rm mariadb_server

2️⃣ Crear un contenedor con volumen persistente:

docker run -d \
  --name mariadb_server \
  -e MYSQL_ROOT_PASSWORD=mi_contraseña \
  -e MYSQL_DATABASE=mi_base_de_datos \
  -e MYSQL_USER=mi_usuario \
  -e MYSQL_PASSWORD=usuario_contraseña \
  -p 3307:3306 \
  -v C:\docker-mariadb:/var/lib/mysql \
  mariadb:10.6