# Curso Fundamentos de Docker - Azul School 🐳

Bienvenido a mi repositorio de notas y prácticas del curso de Docker. Aquí iré documentando los conceptos fundamentales y comandos que vaya aprendiendo.

> **Estado:** 🚧 En curso

## 📖 Tabla de Contenidos

- [Módulo 1: Entendiendo las imágenes](#módulo-1-entendiendo-las-imágenes)

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
