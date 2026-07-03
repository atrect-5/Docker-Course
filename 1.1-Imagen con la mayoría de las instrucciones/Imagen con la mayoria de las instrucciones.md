# 🚀 Práctica: Servidor Nginx con Múltiples Instrucciones

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