# Módulo 2: Trabajando con contenedores

En este módulo se realizaron prácticas directas con imágenes oficiales de Docker Hub, aprendiendo a desplegar, configurar e interactuar con servicios directamente desde la CLI.

---

## 1. Servidor Apache (Imagen oficial `httpd`)

Se utilizó este comando para levantar un servidor web básico:
```bash
docker run -d -p 80:80 --name apache-test httpd
```

### Observaciones del proceso:
1. **Descarga automática**: Si la imagen no se encuentra localmente, Docker la descarga de Docker Hub mostrando:
   - `Unable to find image 'httpd:latest' locally`
   - `latest: Pulling from library/httpd`
2. **Verificación**: Al acceder a `http://localhost:80` en el navegador, se visualiza el texto **"It works!"**.

### Exploración interna:
Para ejecutar una terminal interactiva dentro del contenedor:
```bash
docker exec -it apache-test bash
```
*   **Ruta de archivos**: `/usr/local/apache2/htdocs/`
*   **Comando para ver el index**: `cat index.html`
*   **Salida**: Escribir `exit` para volver a la consola local.

---

## 2. Base de Datos MySQL

Uso de la imagen oficial con el flag `-e` para configurar variables de entorno (credenciales y base de datos inicial).

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
1. Entrar con el usuario creado:
   ```bash
   docker exec -it mysql-test mysql -u docker-user -p
   ```
2. Consultar bases de datos existentes:
   ```sql
   show databases;
   ```
   *(Aquí se puede verificar que aparece `docker-db`)*.
3. También se puede acceder con el usuario `root` para obtener permisos totales:
   ```bash
   docker exec -it mysql-test mysql -u root -p
   ```

---

## 3. Base de Datos MongoDB (NoSQL)

Despliegue de un contenedor NoSQL:
```bash
docker run -d -p 27017:27017 --name mongo-test mongo
```

### Acceso y verificación:
Para versiones recientes (6.0+), se utiliza el shell `mongosh`:
```bash
docker exec -it mongo-test mongosh
```
> **Nota**: En versiones antiguas se usaba el comando `mongo`, pero actualmente se recomienda el uso de imágenes modernas y `mongosh`.

Comando de prueba dentro de `mongosh`: `show dbs`

---

## 📌 Conclusiones y Tips
- **Documentación oficial**: Es fundamental revisar el apartado "How to use this image" en Docker Hub para conocer variables obligatorias (como en MySQL) y puertos expuestos.
- **Persistencia**: Aunque estas pruebas son volátiles, en proyectos reales se deben usar **Volúmenes** para que la información no se pierda al eliminar el contenedor.
- **Flexibilidad**: Docker permite probar software completo (MySQL, MongoDB, Apache) en segundos sin necesidad de instalar todo el paquete en el sistema operativo host.