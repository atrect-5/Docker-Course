# Prácticas de Docker Compose

Para iniciar con Docker Compose, lo primero que debemos hacer es crear un archivo donde guardaremos las configuraciones. Este archivo debe tener la extensión `.yml`.
En este archivo se incluirán las configuraciones de los servicios que queremos levantar, como por ejemplo la imagen que se va a usar, los puertos que se van a exponer, las variables de entorno, entre otras cosas.
Así como se puede ver en el archivo: `5-Docker Compose/1-Creando un archivo docker-compose/docker-compose.yml`

Una vez que se crea este archivo de configuración, se levanta el servicio de Compose con el siguiente comando:
```bash
docker compose up -d
```
**Nota:** Al ejecutar esto en la consola, se debe estar en el mismo directorio del archivo `.yml`. De lo contrario, se debe especificar la ruta a este archivo con el flag `-f` (Este flag va antes de `up`).
**Nota:** Aquí el flag `-d` también permite la ejecución en segundo plano, pero debe colocarse después de `up`.

Cuando se ejecuta este comando en la consola, verás que se crean los contenedores y también una red para la comunicación entre contenedores.

Los contenedores que se crean con Docker Compose también se pueden listar con el comando en consola:
```bash
docker compose ps
```
**Nota:** A diferencia de `docker ps` (que es global), para este comando debes estar en el mismo directorio que el archivo de configuración para que Docker sepa qué proyecto consultar.

Si utilizamos este comando cuando el Compose está activo (dentro del mismo directorio), detendrá los servicios de Docker Compose y después los eliminará, así como eliminará la red que se creó:
```bash
docker compose down
```

---

## 1. Variables de Entorno con common.env

Las variables de entorno se pueden configurar directamente en el archivo YAML, o también se pueden configurar utilizando un archivo `common.env` (el nombre puede ser cualquiera, pero debe tener extensión `.env`).
Esto se puede observar en los archivos:
- `5-Docker Compose/2-Variables de entorno con env/docker-compose.yml`
- `5-Docker Compose/2-Variables de entorno con env/common.env`

Aquí lo que sucede es que, en lugar de asignarle directamente las variables de entorno en el archivo de configuración de Compose, se agregan en un archivo `.env` y después se usa ese archivo para asignar las variables de entorno.
Así que cuando haces eso y luego levantas los servicios con:
```bash
docker compose up -d
```
Se aprecia que igualmente se crean los contenedores, así como la red de manera correcta.

Y de igual manera se pueden apreciar las variables de entorno (así como se verían al asignarlas en el mismo YAML):
```bash
docker exec -ti my-db-env bash
```
Dentro del contenedor:
```bash
bash-5.1# env
```
**Output:**
```
MYSQL_ROOT_PASSWORD=admin
MYSQL_PASSWORD=docker-password
MYSQL_USER=docker-user
MYSQL_DATABASE=docker-compose-db
```
Y como podemos ver, en medio de las variables de entorno que nos arroja el comando `env` en bash, podemos ver que se asignan correctamente nuestras variables de entorno.

---

## 2. Volúmenes en Docker Compose

Aquí también podemos usar volúmenes dentro de nuestros contenedores para poder persistir los datos de nuestros contenedores.
De igual manera se pueden usar bind mounts o volúmenes nombrados, como en un contenedor normal.
Para configurarlo, se puede hacer como se muestra en: `5-Docker Compose/3-Volumenes en Docker Compose/docker-compose.yml`
En este se crea el servicio de `db` al que se le asigna un volumen nombrado creado ahí mismo.

Para comprobarlo, una vez levantado el Docker Compose (recordando estar en el mismo directorio cuando se ejecuta el comando):
```bash
docker compose up -d
```

Podemos ver la lista de volúmenes que tenemos en Docker y ver nuestro nuevo volumen ahí:
```bash
docker volume ls
```
**Output:**
```
local     3-volumenesendockercompose_mysql_data_volume
```
(Como se puede apreciar, Docker Compose agrega el nombre del directorio actual sin espacios al nombre del volumen creado).

Se puede probar que se guardan los datos creando unos cuantos registros en una tabla de nuestro contenedor:
```bash
docker exec -ti my-db-vol mysql -p
```
**Enter password:** (Ingresar `docker-password`)
```sql
use docker-compose-db;
CREATE TABLE animals (name VARCHAR(30));
INSERT INTO animals (name) VALUES ('Leopardo'),('Oso');
SELECT * FROM animals;
exit
```
**Output:**
```
+----------+
| name     |
+----------+
| Leopardo |
| Oso      |
+----------+
```

Después lo que haremos será parar los servicios:
```bash
docker compose down
```

Y si los volvemos a levantar, veremos que se volvió a cargar la información:
```sql
use docker-compose-db;
SELECT * FROM animals;
```
**Output:**
```
+----------+
| name     |
+----------+
| Leopardo |
| Oso      |
+----------+
```

### Consideraciones sobre Volúmenes en Docker Compose:
- Si se usa el flag `-v` de esta manera `docker compose down -v`, también eliminará los volúmenes y se perderá la información.
- Debido a que cada vez que se crea el volumen se le coloca de prefijo el nombre del directorio padre; si cambias el nombre del directorio del proyecto, no accede al mismo volumen.
- Si usas el flag `-p` para cambiar el nombre del proyecto `docker compose -p otro up`, tampoco encontrará el volumen.
- Usando `name: mysql_data_volume` puedes mantener el nombre del volumen igual y evitar que se le asigne un prefijo.
- Si se crea un volumen de bind mount, se debe eliminar el directorio manualmente ya que `-v` no lo detecta.

---

## 3. Redes en Docker Compose

Docker Compose también nos permite configurar redes personalizadas para nuestros servicios, lo cual es muy útil para controlar la comunicación entre los contenedores.
Para configurar las redes, se puede hacer como se muestra en: `5-Docker Compose/4-Redes en Docker Compose/docker-compose.yml`
Aquí se crea una red personalizada que es la que va a utilizar nuestro servicio.

Para apreciar la red creada, una vez levantado el Docker Compose (recordando estar en el mismo directorio cuando se ejecuta el comando):
```bash
docker compose up -d
```
Aquí se mostrará que se ha creado nuestra red personalizada.

Inclusive se puede ver listada en la lista de redes de Docker:
```bash
docker network ls
```

### Consideraciones sobre Redes en Docker Compose:
- Si no se especifica una red personalizada, Docker Compose crea una red por defecto para los servicios.
- Al crear una red personalizada, los servicios que se conecten a esa red podrán comunicarse entre sí utilizando el nombre del servicio como hostname.
- Si se quiere conectar un servicio a varias redes, se pueden especificar todas las redes a las que se quiere conectar en la configuración del servicio.
- Si se tiene al menos un servicio al que no se le asigna una red personalizada, ese servicio se conectará a la red por defecto creada por Docker Compose, pero no se crea una red `default` si todos los servicios tienen una red personalizada asignada.
- Al hacer un `docker compose down` se eliminan las redes personalizadas creadas por Docker Compose, pero no se eliminan las redes que ya existían antes de levantar el Compose.

---

## 📌 Conclusiones y Tips

Estos son ejemplos de las configuraciones básicas que se pueden implementar en los contenedores que forman parte de un Docker Compose, pero también se pueden configurar otras cosas como por ejemplo los comandos que se ejecutan al iniciar el contenedor, las dependencias entre servicios, límites de recursos, entre otras cosas.