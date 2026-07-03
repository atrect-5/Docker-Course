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

### 🚀 Práctica: Administración de Usuarios (Dockerfile propio)
Se trabajó en la seguridad y permisos del contenedor mediante la creación de un usuario no-root dentro de una imagen personalizada.

**Pasos realizados:**
1.  **Preparación**: Navegar al directorio donde se encuentra el Dockerfile:
    ```bash
    cd "2-Trabajando con contenedores"
    ```
2.  **Dockerfile**: Se utilizó una base de `ubuntu:latest`, se definió una variable de entorno y se creó el usuario `usuario-prueba`.
3.  **Construcción**: `docker build -t ubuntu:prueba .`
4.  **Ejecución**:
    ```bash
    docker run -d -it --name ubuntu-test ubuntu:prueba
    ```
    *Nota: Se usa `-it` para mantener el proceso de la shell activo en una imagen de SO.*
5.  **Verificación**:
    - Acceso por defecto: `docker exec -it ubuntu-test bash` (Inicia sesión como `usuario-prueba`).
    - Acceso administrativo: 
      ```bash
      docker exec -it -u root ubuntu-test bash
      ```
      *Esto permite saltar la restricción del usuario por defecto para tareas de mantenimiento.*

---

### 🚀 Práctica: Limitación de Recursos
Aprendí a restringir el uso de hardware de los contenedores para asegurar la estabilidad del sistema host y evitar el agotamiento de recursos.

**Pasos realizados:**
1.  **Monitoreo inicial**: Se revisó el consumo del contenedor `ubuntu-test` (Creado en la práctica anterior) usando `docker stats`. Se observó que, por defecto, el límite de memoria era el total disponible en el host (ej. 7.379GiB).
2.  **Creación con límites**: Se ejecutó un nuevo contenedor basado en la misma imagen, pero restringiendo su memoria a 100MB usando el flag `-m`.
    ```bash
    docker run -d -ti -m "100mb" --name ubuntu-test2 ubuntu:prueba
    ```
3.  **Verificación**: Se compararon ambos contenedores en tiempo real:
    ```bash
    docker stats ubuntu-test2
    ```

**Resultado**: El campo `MEM USAGE / LIMIT` confirmó el límite de `100MiB`, demostrando que Docker gestiona correctamente el aislamiento de recursos y evita que un contenedor consuma más de lo asignado.

---

## 📌 Conclusiones y Tips
- **Documentación oficial**: Es fundamental revisar el apartado "How to use this image" en Docker Hub para conocer variables obligatorias (como en MySQL) y puertos expuestos.
- **Persistencia**: Aunque estas pruebas son volátiles, en proyectos reales se deben usar **Volúmenes** para que la información no se pierda al eliminar el contenedor.
- **Flexibilidad**: Docker permite probar software completo (MySQL, MongoDB, Apache) en segundos sin necesidad de instalar todo el paquete en el sistema operativo host.