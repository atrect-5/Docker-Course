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
1.  **`index.html`**: Se creó una página web estática con HTML y CSS interno para ser usada como página de prueba.
2.  **Variables y Usuarios**: Se implementó lógica en el Dockerfile para generar archivos HTML dinámicamente (`variable.html`, `usuario1.html`, `usuario2.html`) demostrando el uso de `ENV` y el cambio de permisos con `USER`.
3.  **Configuración del Servidor**: Se definieron metadatos con `LABEL`, un directorio de trabajo con `WORKDIR` y un volumen para logs con `VOLUME`.

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
