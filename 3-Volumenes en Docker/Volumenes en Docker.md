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

---

## 4. Compartir volúmenes entre contenedores

A continuación, veremos de manera sencilla cómo dos contenedores pueden utilizar el mismo volumen simultáneamente.

Empezaremos por crear un volumen:

```bash
docker volume create volumen-test
```

Después, crearemos dos contenedores de Ubuntu que utilizarán este mismo volumen:

```bash
docker run -dti -v volumen-test:/opt --name compartir-volumen1 ubuntu
docker run -dti -v volumen-test:/opt --name compartir-volumen2 ubuntu
```

Una vez creados los contenedores, accederemos a ellos utilizando dos terminales diferentes (una para cada contenedor):

```bash
docker exec -it compartir-volumen1 bash
```
---
```bash
docker exec -it compartir-volumen2 bash
```

Ahora, en cualquiera de las dos terminales de los contenedores a los que ingresamos, realizaremos una prueba. Para esto, crearemos un archivo en el directorio montado:

**Terminal 1:**
```bash
root@e2173848710a:/# echo "Ejemplo de compartir volumen" > /opt/Ejemplo.txt
```

En la consola del segundo contenedor, verificaremos que el archivo se creó correctamente con el contenido generado en la Terminal 1:

**Terminal 2:**
```bash
root@fa51771c39c9:/# cat /opt/Ejemplo.txt
```
**Output:** `Ejemplo de compartir volumen`

Con esta prueba, comprobamos cómo ambos contenedores están compartiendo y utilizando el mismo volumen de datos de forma efectiva.

---

## 📌 Conclusiones y Tips

- **Flexibilidad de montaje**: Un solo contenedor puede utilizar múltiples volúmenes de distintos tipos (bind mounts, volúmenes nombrados, etc.) de forma simultánea, permitiendo separar, por ejemplo, los datos de la aplicación de los archivos de logs.

- **Compartición y Riesgos**: Es posible que varios contenedores utilicen el mismo volumen simultáneamente. Si bien es una herramienta poderosa para compartir información entre servicios, se debe tener precaución (especialmente con bases de datos) para evitar problemas de concurrencia o corrupción de datos si intentan escribir al mismo tiempo sin la gestión adecuada.

- **Persistencia de configuración**: Al reutilizar un volumen (ya sea bind mount o nombrado) en un nuevo contenedor de base de datos, las variables de entorno iniciales (como contraseñas o nombres de DB) dejan de ser estrictamente necesarias, ya que el estado y la configuración ya existen dentro del volumen persistido.

- **Compatibilidad en WSL2**: Al trabajar con *Bind Mounts* en entornos Windows con WSL2, es recomendable evitar rutas montadas sobre `/mnt/*` para servicios exigentes como MySQL, ya que pueden presentar errores de permisos o rendimiento. Es mejor utilizar la estructura de archivos nativa de la distribución de Linux.

- **Administración nativa**: Los volúmenes nombrados son más fáciles de administrar y rastrear que los anónimos, ya que Docker los gestiona internamente y permite identificarlos rápidamente mediante el comando `docker volume ls`.

- **Higiene del sistema**: Es una buena práctica realizar limpiezas periódicas de volúmenes "huérfanos" (dangling) que ya no están asociados a ningún contenedor para liberar espacio en disco.

---