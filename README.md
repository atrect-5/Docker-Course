# Curso Fundamentos de Docker - Azul School 🐳

Bienvenido a mi repositorio de notas y prácticas del curso de Docker. Aquí iré documentando los conceptos fundamentales y comandos que vaya aprendiendo.

> **Estado:** 🚧 En curso

## 📖 Tabla de Contenidos

- [Módulo 1: Entendiendo las imágenes](#módulo-1-entendiendo-las-imágenes)
- [Módulo 2: Trabajando con contenedores](#módulo-2-trabajando-con-contenedores)
- [Módulo 3: Volúmenes en Docker](#módulo-3-volúmenes-en-docker)

---

## Módulo 1: Entendiendo las imágenes

En este módulo introductorio, aprendí los conceptos base de la arquitectura de Docker y el ciclo de vida de las imágenes.

### 🧠 Conceptos Clave
- **Arquitectura Cliente-Servidor:** Cómo interactúan el Docker Client, el Daemon y los Registries.
- **Imágenes vs Contenedores:** La diferencia entre la plantilla inmutable (imagen) y la instancia ejecutable (contenedor).
- **Dockerfile:** La "receta" para construir imágenes capa por capa.
- **.dockerignore:** Archivo para excluir archivos y directorios del contexto de construcción de la imagen, optimizando su tamaño y seguridad.

### 📝 Instrucciones Clave del Dockerfile
Estas son las instrucciones principales que he aprendido para construir imágenes:
- `FROM`: Define la imagen base.
- `LABEL`: Añade metadatos a la imagen (autor, sitio web, etc.).
- `ENV`: Configura variables de entorno.
- `WORKDIR`: Establece el directorio de trabajo dentro del contenedor.
- `RUN`: Ejecuta comandos durante la construcción de la imagen.
- `USER`: Cambia el usuario activo para ejecutar instrucciones (seguridad y permisos).
- `COPY`: Copia archivos/directorios desde el host al sistema de archivos de la imagen.
- `VOLUME`: Crea un punto de montaje para persistencia de datos (ej. logs).
- `EXPOSE`: Documenta el puerto en el que escucha la aplicación.
- `CMD`: Especifica el comando por defecto a ejecutar cuando el contenedor inicia.

### 📄 Recursos
He recopilado una lista detallada de comandos de la CLI y definiciones de las instrucciones del Dockerfile en el siguiente archivo:
- **Notas importantes de Docker.txt**

### 🚀 Práctica: Servidor Apache con Página Personalizada
Como parte de la práctica de este módulo, se creó una imagen personalizada de Ubuntu con un servidor Apache que sirve una página web estática.

**Avances Realizados:**
1.  **`.dockerignore`**: Se ha añadido un archivo `.dockerignore` para excluir ficheros no necesarios (como `node_modules`) del contexto de construcción, optimizando así el tamaño y la seguridad de la imagen.
2.  **`index.html`**: Se creó una página web estática con HTML y CSS interno para ser usada como página de prueba.
3.  **Variables y Usuarios**: Se implementó lógica en el Dockerfile para generar archivos HTML dinámicamente (`variable.html`, `usuario1.html`, `usuario2.html`) demostrando el uso de `ENV` y el cambio de permisos con `USER`.
4.  **Configuración del Servidor**: Se definieron metadatos con `LABEL`, un directorio de trabajo con `WORKDIR` y un volumen para logs con `VOLUME`.

#### Cómo Replicar Este Proyecto

Sigue estos pasos para construir la imagen y ejecutar el contenedor en tu propia máquina.

**Paso 1: Construir la Imagen de Docker**

1.  Abre una terminal.
2.  Navega a la carpeta que contiene el `Dockerfile`:
    ```bash
    cd "1-Entendiendo las imagenes"
    ```
3.  Ejecuta el siguiente comando para construir la imagen. Le asignaremos el nombre (`-t`) `servidor-apache:1.0`.
    ```bash
    docker build -t servidor-apache:1.0 .
    ```

**Paso 2: Crear y Ejecutar el Contenedor**

1.  Una vez construida la imagen, ejecuta este comando para iniciar un contenedor a partir de ella:
    ```bash
    docker run -d -p 8080:80 --name mi-servidor-web servidor-apache:1.0
    ```
2.  **Explicación de los flags:**
    -   `-d` (detached): Ejecuta el contenedor en segundo plano.
    -   `-p 8080:80`: Mapea el puerto 8080 de tu máquina (host) al puerto 80 del contenedor (donde Apache está escuchando).
    -   `--name mi-servidor-web`: Le da un nombre fácil de recordar a tu contenedor.

**Paso 3: Verificar el Resultado**

1.  Abre tu navegador web preferido.
2.  Visita la dirección `http://localhost:8080`. Deberías ver la página de bienvenida.
3.  **Prueba los enlaces:** Haz clic en los enlaces de la página para verificar la generación de archivos mediante variables de entorno y los distintos usuarios (`root` y `alex`).

**Paso 4: Detener y Eliminar el Contenedor (Opcional)**
Cuando hayas terminado de experimentar, puedes detener y eliminar el contenedor para limpiar tu sistema:
```bash
# Detener el contenedor
docker stop mi-servidor-web

# Eliminar el contenedor
docker rm mi-servidor-web
```

---

### 🚀 Práctica: Servidor Nginx con Múltiples Instrucciones

Esta práctica avanzada utiliza una imagen `debian:latest` para construir un servidor Nginx. El `Dockerfile` está diseñado para demostrar el uso combinado de la mayoría de las instrucciones aprendidas, creando un entorno más realista.

**Avances Realizados:**
1.  **Instalación de Nginx**: Se utiliza `RUN` para actualizar el sistema e instalar el servidor Nginx desde los repositorios de Debian.
2.  **Gestión de Usuarios**: Se crea un usuario no-root llamado `alex` con `USER` para ejecutar comandos, mejorando la seguridad.
3.  **Variables de Entorno y Ficheros Dinámicos**: Se usa `ENV` para crear una variable y luego se escribe en un fichero (`envi.html`) dentro del contenedor.
4.  **Permisos y Copia de Ficheros**: Se demuestra el cambio entre `USER root` y `USER alex` para gestionar permisos en la creación y copia de ficheros (`username.html`).
5.  **Instrucciones de Metadatos y Red**: Se emplean `LABEL`, `EXPOSE` y `VOLUME` para añadir metadatos, documentar el puerto y definir un volumen para logs.

#### Cómo Replicar Este Proyecto

**Paso 1: Construir la Imagen de Docker**

1.  Abre una terminal.
2.  Navega a la nueva carpeta de la práctica:
    ```bash
    cd "1.1-Imagen con la mayoría de las instrucciones"
    ```
3.  Ejecuta el siguiente comando para construir la imagen. El nombre será `servidor-nginx:1.0`.
    ```bash
    docker build -t servidor-nginx:1.0 .
    ```

**Paso 2: Crear y Ejecutar el Contenedor**

1.  Una vez construida la imagen, ejecuta este comando para iniciar el contenedor:
    ```bash
    docker run -d -p 8081:80 --name mi-servidor-nginx servidor-nginx:1.0
    ```
2.  **Nota**: Usamos el puerto `8081` en el host para no entrar en conflicto con la práctica anterior.

**Paso 3: Verificar el Resultado**

1.  Abre tu navegador y visita `http://localhost:8081`.
2.  **Prueba los enlaces**: Haz clic en los enlaces para verificar los ficheros `envi.html` y `username.html` generados durante la construcción de la imagen.

**Paso 4: Detener y Eliminar el Contenedor (Opcional)**
```bash
# Detener el contenedor
docker stop mi-servidor-nginx

# Eliminar el contenedor
docker rm mi-servidor-nginx
```

---

## Módulo 2: Trabajando con contenedores

En este módulo, el enfoque fue totalmente práctico, trabajando directamente con imágenes oficiales del Docker Hub para desplegar servicios comunes y aprender a interactuar con ellos desde la CLI.

### 🚀 Práctica: Servidor Apache (Imagen Oficial `httpd`)
Se utilizó la imagen oficial de Apache para entender el flujo de descarga automática y exploración de archivos internos.

**Pasos realizados:**
1.  **Ejecución**: `docker run -d -p 80:80 --name apache-test httpd`
2.  **Exploración**: Se usó `docker exec -it apache-test bash` para entrar al contenedor.
3.  **Ruta de archivos**: Los archivos servidos se encuentran en `/usr/local/apache2/htdocs/`.

---

### 🚀 Práctica: Base de Datos MySQL
Uso de variables de entorno (`-e`) para configurar credenciales y bases de datos iniciales.

**Configuración y Ejecución:**
```bash
docker run -d \
  -e "MYSQL_ROOT_PASSWORD=admin" \
  -e "MYSQL_DATABASE=docker-db" \
  -e "MYSQL_USER=docker-user" \
  -e "MYSQL_PASSWORD=dockerpassword" \
  -p 3306:3306 \
  --name mysql-test mysql
```

**Verificación:**
1.  Acceder al cliente MySQL dentro del contenedor:
    ```bash
    docker exec -it mysql-test mysql -u docker-user -p
    ```
2.  Comprobar base de datos: `show databases;`

---

### 🚀 Práctica: Base de Datos MongoDB
Implementación de NoSQL y uso del shell moderno `mongosh`.

**Pasos realizados:**
1.  **Ejecución**: `docker run -d -p 27017:27017 --name mongo-test mongo`
2.  **Interacción**:
    - Para versiones actuales (6.0+): `docker exec -it mongo-test mongosh`
    - Para versiones antiguas: `docker exec -it mongo-test mongo`
3.  **Comando de prueba**: `show dbs` para listar bases de datos.

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

### 💡 Lecciones Aprendidas y Tips

- **Documentación en Docker Hub**: Es vital revisar la sección "How to use this image" de cada imagen oficial, ya que ahí se especifican las variables de entorno necesarias (como en MySQL) y los puertos por defecto.
- **Persistencia**: Aunque en estas prácticas los datos son efímeros, en entornos reales debemos usar **Volúmenes** para que la información de las bases de datos no se pierda al eliminar el contenedor.
- **Interactividad**: El flag `-it` en `docker exec` es nuestra puerta de entrada para depurar y administrar servicios "desde adentro".

---

## Módulo 3: Volúmenes en Docker

En este módulo se profundizó en la persistencia de datos, aprendiendo a gestionar la información para que trascienda el ciclo de vida de los contenedores y evitar la pérdida de datos críticos.

### 📄 Recursos
El desarrollo detallado de los pasos seguidos, comandos ejecutados y resultados de estas prácticas se especifica en:
- **[Volúmenes en Docker.md](./3-Volumenes%20en%20Docker/Volumenes%20en%20Docker.md)**
  - *Ruta:* `3-Volumenes en Docker\Volumenes en Docker.md`

---

### 🚀 Práctica: El Riesgo de los Volúmenes Anónimos
Se realizó una prueba con MySQL donde se comprobó que, al no especificar un volumen, Docker crea uno anónimo de forma automática. 
**Resultado:** Al eliminar el contenedor, el volumen anónimo también desaparece, provocando la pérdida total de la base de datos creada.

---

### 🚀 Práctica: Persistencia con Bind Mounts (Volumen de Host)
Se utilizó un directorio específico del host para mapearlo directamente al contenedor de MySQL.

**Puntos clave:**
- Se vinculó una ruta local (`~/docker-volumes/mysql`) con `/var/lib/mysql` dentro del contenedor.
- Se verificó que, incluso borrando el contenedor, los archivos de la base de datos permanecen en el host y pueden ser reutilizados por nuevos contenedores.

---

### 🚀 Práctica: Volúmenes Nombrados (Named Volumes)
Se implementó la gestión nativa de Docker para la persistencia, creando volúmenes que Docker administra internamente.

**Comandos clave:**
- `docker volume create mysql-test-volumen`
- Administración: `docker volume ls` y `docker volume inspect`.
- **Ventaja:** Mayor portabilidad y facilidad de administración que los Bind Mounts.

---

### 🚀 Práctica: Compartir Datos entre Contenedores
Se demostró la capacidad de Docker para que múltiples contenedores accedan a la misma fuente de datos.

**Pasos realizados:**
1. Se creó un volumen común llamado `volumen-test`.
2. Se iniciaron dos contenedores de Ubuntu montando dicho volumen en `/opt`.
3. Se comprobó que un archivo creado en el **Contenedor 1** era inmediatamente visible y editable por el **Contenedor 2**, facilitando la colaboración entre servicios.

---

### 💡 Lecciones Aprendidas y Tips

- **Persistencia Crítica**: Los volúmenes son obligatorios para aplicaciones con estado (bases de datos, logs, uploads).
- **Diferenciación**: Los *Bind Mounts* son ideales para desarrollo (mapeo de código), mientras que los *Named Volumes* son preferibles en entornos de producción.
- **Higiene del Sistema**: Aprendí a usar `docker volume prune` para eliminar volúmenes "huérfanos" (dangling) que ya no están asociados a ningún contenedor y solo consumen espacio.
- **Flexibilidad**: Un contenedor puede usar múltiples volúmenes de distintos tipos simultáneamente, permitiendo separar, por ejemplo, los datos de la base de datos de los logs.

---

## 🛠️ Comandos de Limpieza (Cheat Sheet)

Durante las prácticas se crean muchos contenedores. Aquí los comandos para mantener el sistema limpio:

```bash
# Detener todos los contenedores
docker stop $(docker ps -aq)

# Eliminar todos los contenedores
docker rm $(docker ps -aq)

# Limpieza profunda (contenedores, redes e imágenes sin uso)
docker system prune
```
