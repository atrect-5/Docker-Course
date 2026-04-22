# Prácticas de Volúmenes en Docker

## 1. Probando sin volúmenes (Anónimo por defecto en MySQL)

Para este ejercicio, lo primero que haremos es crear un contenedor de MySQL (comando utilizado previamente en el curso):

```bash
docker run -d \
  -e MYSQL_ROOT_PASSWORD=admin \
  -e MYSQL_DATABASE=docker-db \
  -e MYSQL_USER=docker-user \
  -e MYSQL_PASSWORD=dockerpassword \
  -p 3306:3306 \
  --name mysql-test \
  mysql
```

Después de haber creado este contenedor, nos conectaremos a él:

```bash
docker exec -it mysql-test mysql -u docker-user -p
```
**Enter password:** (Ingresar `dockerpassword`)

Una vez dentro, seleccionamos la base de datos que definimos al crear el contenedor:
```sql
use docker-db;
```

Posteriormente, creamos una tabla de prueba y agregamos datos:
```sql
CREATE TABLE users (name VARCHAR(30));
INSERT INTO users (name) VALUES ('Alejandro');
```

Podemos comprobar que se agregó el registro de la siguiente manera:
```sql
SELECT * FROM users;
```

Salimos del contenedor:
```sql
exit 
```

Y finalmente detenemos y eliminamos el contenedor:
```bash
docker stop mysql-test
docker rm -f mysql-test
```

---

Sin embargo, una vez eliminado, notaremos que ya no podemos acceder a la información creada. 
Si volvemos a crear el contenedor (paso 1) e ingresamos nuevamente (paso 2), al intentar visualizar la tabla, veremos que ya no existe:

```sql
use docker-db;
show tables;
```
**Output:** `Empty set (0.001 sec)`

Esto sucede porque, al no especificar el volumen donde queremos guardar la información, se crea un **volumen anónimo** con un nombre aleatorio que Docker gestiona automáticamente. Al eliminar el contenedor, este volumen anónimo también se borra, provocando la pérdida de todos los datos persistidos en él.

---

## 2. Bind mounts / Volumen de host

En el Volumen de Host (Bind mounts), un directorio del host se mapea directamente a un directorio del contenedor.  

Para mantener la información guardada en un contenedor, podemos utilizar Bind mounts. Primero debemos definir el directorio de nuestro host donde se guardará este volumen:

```bash
mkdir -p ~/docker-volumes/mysql
```
*(**NOTA:** MySQL + bind mounts sobre `/mnt/*` en WSL2 es una combinación problemática, por lo que el directorio se creó dentro de la estructura de archivos de WSL2).*

Después, al crear el contenedor, debemos agregar la opción `-v` para mapear el directorio del host al directorio del contenedor donde se almacenará la información de MySQL (consultar la documentación para identificar el directorio correcto de la imagen):

```bash
docker run -d \
  -e MYSQL_ROOT_PASSWORD=admin \
  -e MYSQL_DATABASE=docker-db \
  -e MYSQL_USER=docker-user \
  -e MYSQL_PASSWORD=dockerpassword \
  -p 3306:3306 \
  -v /home/atrect5/docker-volumes/mysql:/var/lib/mysql \
  --name mysql-test-bind-mount \
  mysql
```

La primera ruta después del flag `-v` es nuestro directorio en el host; la ruta después de los dos puntos `:` es la ubicación dentro del contenedor que se mapeará para persistir los datos.  

Una vez realizado esto, podemos dirigirnos al directorio creado en el host para observar cómo se generaron varios archivos y carpetas, que es donde reside la información persistente.

Con el contenedor en ejecución, repetiremos el proceso de conexión para crear una tabla en la base de datos `docker-db` y agregar información:

```bash
docker exec -it mysql-test-bind-mount mysql -u docker-user -p
```
**Enter password:** (Ingresar `dockerpassword`)

```sql
use docker-db;
CREATE TABLE users (name VARCHAR(30));
INSERT INTO users (name) VALUES ('Alejandro');
SELECT * FROM users;
exit
```

Detenemos y eliminamos el contenedor:
```bash
docker stop mysql-test-bind-mount
docker rm -f mysql-test-bind-mount
```

Aún con el contenedor eliminado, la información permanece en el directorio del host, incluyendo la base de datos creada.

---

Para verificar la persistencia, crearemos un nuevo contenedor utilizando el mismo directorio:

```bash
docker run -d \
  -p 3306:3306 \
  -v /home/atrect5/docker-volumes/mysql:/var/lib/mysql \
  --name mysql-test-bind-mount \
  mysql
```
**NOTA:** En este caso, ya no son estrictamente necesarias las variables de entorno iniciales, ya que la configuración y los datos ya existen en el volumen.

Verificamos que la información siga ahí ingresando a MySQL:
```bash
docker exec -it mysql-test-bind-mount mysql -u docker-user -p
```
**Enter password:** (Ingresar `dockerpassword`)

```sql
use docker-db;
SELECT * FROM users;
```

Confirmamos que tenemos acceso a la información del contenedor eliminado anteriormente.

**NOTA:** Varios contenedores pueden usar el mismo volumen, lo cual es útil para compartir datos, pero puede causar problemas de concurrencia o corrupción si intentan escribir simultáneamente sin la gestión adecuada.

---

## 3. Volúmenes nombrados

Para utilizar un volumen con nombre, lo primero es crearlo en Docker (nosotros asignamos el nombre, pero Docker gestiona su ubicación internamente):

```bash
docker volume create mysql-test-volumen
```
Para verificar su creación, usamos `docker volume ls`.

Una vez creado, iniciamos el contenedor referenciando este nombre en el flag `-v`:

```bash
docker run -d \
  -e MYSQL_ROOT_PASSWORD=admin \
  -e MYSQL_DATABASE=docker-db \
  -e MYSQL_USER=docker-user \
  -e MYSQL_PASSWORD=dockerpassword \
  -p 3306:3306 \
  -v mysql-test-volumen:/var/lib/mysql \
  --name mysql-test-volumen-nombrado \
  mysql
```

Nos conectaremos al contenedor para insertar datos de prueba:

```bash
docker exec -it mysql-test-volumen-nombrado mysql -u docker-user -p
```
**Enter password:** (Ingresar `dockerpassword`)

```sql
use docker-db;
CREATE TABLE animals (name VARCHAR(30));
INSERT INTO animals (name) VALUES ('Leopardo');
SELECT * FROM animals;
exit 
```

Detenemos y eliminamos el contenedor:
```bash
docker stop mysql-test-volumen-nombrado
docker rm -f mysql-test-volumen-nombrado
```

---

Al eliminar el contenedor, la información se mantiene en el volumen nombrado. Este tipo de volumen es más fácil de administrar, ya que aparece explícitamente en el listado de `docker volume ls`. 

Lo comprobamos creando otro contenedor que reutilice el volumen:

```bash
docker run -d \
  -p 3306:3306 \
  -v mysql-test-volumen:/var/lib/mysql \
  --name mysql-test-volumen-nombrado \
  mysql
```

Ingresamos a MySQL y verificamos los datos:
```bash
docker exec -it mysql-test-volumen-nombrado mysql -u docker-user -p
```
**Enter password:** (Ingresar `dockerpassword`)

```sql
use docker-db;
SELECT * FROM animals;
```

Confirmamos que los datos persisten. 

Para eliminar este volumen, debemos asegurarnos de que ningún contenedor lo esté utilizando:
```bash
docker volume rm mysql-test-volumen
```
Si el volumen está en uso, Docker mostrará un error impidiendo su eliminación.

---

**NOTA:** Un contenedor puede usar varios volumenes diferentes y de distintos tipos (bind mounts, volúmenes nombrados, etc.) al mismo tiempo, lo que permite una gran flexibilidad en la gestión de datos persistentes.