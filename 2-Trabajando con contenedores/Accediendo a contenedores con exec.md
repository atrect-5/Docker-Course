# Módulo 2: Trabajando con contenedores

En este módulo se realizaron prácticas directas con imágenes oficiales de Docker Hub, aprendiendo a desplegar, configurar e interactuar con servicios directamente desde la CLI.

---

## 1. Servidor Apache (Imagen oficial `httpd`)

Se utilizó este comando para levantar un servidor web básico utilizando la imagen oficial de Apache HTTP Server:
```bash
docker run -d -p 80:80 --name apache-test httpd
```

### Observaciones del proceso:
1. **Descarga automática**: Si la imagen no se encuentra localmente, Docker la descarga de Docker Hub mostrando:
   - `Unable to find image 'httpd:latest' locally`
   - `latest: Pulling from library/httpd`
2. **Verificación**: Al acceder a `http://localhost:80` en el navegador, se visualiza el texto **"It works!"**, lo que indica que el servidor está funcionando correctamente.

### Exploración interna:
Podemos ejecutar una terminal interactiva dentro del contenedor con el siguiente comando (Tambien es facilmente accesible desde Docker Desktop):
```bash
docker exec -it apache-test bash
```
Esto nos permite inspeccionar el sistema de archivos del contenedor y verificar la ubicación de los archivos servidos por Apache.

*   **Ruta de archivos**: A acceder a la ruta: `/usr/local/apache2/htdocs/`, podremos listar (`ls`) los archivos que Apache está sirviendo.
*   **Ver el index**: Si ejecutamos: `cat index.html`, veremos el contenido del HTML que se muestra en el navegador.
*   **Salida**: Escribir `exit` para volver a la consola local.

---

## 2. Base de Datos MySQL

Ahora haremos uso de la imagen oficial de MySQL, con el flag `-e` para configurar variables de entorno (credenciales y base de datos inicial).

```bash
docker run -d \
  -e "MYSQL_ROOT_PASSWORD=admin" \
  -e "MYSQL_DATABASE=docker-db" \
  -e "MYSQL_USER=docker-user" \
  -e "MYSQL_PASSWORD=dockerpassword" \
  -p 3306:3306 \
  --name mysql-test mysql
```

### Acceso a la base de datos:
1. Podemos acceder a la base de datos con el usuario creado con el siguiente comando:
   ```bash
   docker exec -it mysql-test mysql -u docker-user -p
   ```
2. Dentro de la consola de MySQL, podemos consultar las bases de datos existentes:
   ```sql
   show databases;
   ```
   *(Aquí se puede verificar que aparece `docker-db`, que fue creada al crear el contenedor)*.
3. También se puede acceder con el usuario `root` para obtener permisos totales:
   ```bash
   docker exec -it mysql-test mysql -u root -p
   ```

---

## 3. Base de Datos MongoDB (NoSQL)

Ahora utilizaremos mongo, para crear un contenedor de una base de datos NoSQL:
```bash
docker run -d -p 27017:27017 --name mongo-test mongo
```

### Acceso y verificación:
Para acceder a la consola del contenedor de Mongo en versiones recientes (6.0+), se utiliza el shell `mongosh`:
```bash
docker exec -it mongo-test mongosh
```
> **Nota**: En versiones antiguas se usaba el comando `mongo`, pero actualmente se recomienda el uso de imágenes modernas y `mongosh`.

Si queremos probarlo, podemos listar las bases de datos existentes dentro de la consola de MongoDB:
```bash
show dbs;
```

---

## 📌 Conclusiones y Tips
- **Documentación oficial**: Es fundamental revisar el apartado "How to use this image" en Docker Hub para conocer variables obligatorias (como en MySQL) y puertos expuestos.
- **Persistencia**: Aunque estas pruebas son volátiles, en proyectos reales se deben usar **Volúmenes** para que la información no se pierda al eliminar el contenedor.
- **Flexibilidad**: Docker permite probar software completo (MySQL, MongoDB, Apache) en segundos sin necesidad de instalar todo el paquete en el sistema operativo host.