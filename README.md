# Curso Fundamentos de Docker - Azul School 🐳

Bienvenido a mi repositorio de notas y prácticas del curso de Docker. Aquí iré documentando los conceptos fundamentales y comandos que vaya aprendiendo.

> **Estado:** 🚧 En curso

## 📖 Tabla de Contenidos

- [Módulo 1: Entendiendo las imágenes](#módulo-1-entendiendo-las-imágenes)
- [Módulo 2: Trabajando con contenedores](#módulo-2-trabajando-con-contenedores)
- [Módulo 3: Volúmenes en Docker](#módulo-3-volúmenes-en-docker)
- [Módulo 4: Redes en Docker](#módulo-4-redes-en-docker)
- [Módulo 5: Docker Compose](#módulo-5-docker-compose)

[!NOTE]
> En el archivo de `Notas importantes de Docker.txt` se encuentran todos los comandos de la CLI y definiciones de las instrucciones del Dockerfile, asi como de los conceptos aprendidos en el curso. Este archivo es un resumen de lo aprendido y sirve como referencia rápida.
> Cada módulo tiene su propio archivo de notas y prácticas, que se encuentran en sus respectivas carpetas.

---

## Módulo 1: Entendiendo las imágenes

En este módulo introductorio, aprendí los conceptos base de la arquitectura de Docker y el ciclo de vida de las imágenes.

### 📄 Recursos
El desarrollo detallado de los pasos seguidos, comandos ejecutados y resultados de estas prácticas se especifica en:
- **[Entendiendo las imágenes.md](./1-Entendiendo%20las%20imagenes/Entendiendo%20las%20imagenes.md)**
  - *Ruta:* `1-Entendiendo las imagenes\Entendiendo las imagenes.md`
- **[Imagen con la mayoría de las instrucciones.md](./1.1-Imagen%20con%20la%20mayor%C3%ADa%20de%20las%20instrucciones/Imagen%20con%20la%20mayoria%20de%20las%20instrucciones.md)**
  - *Ruta:* `1.1-Imagen con la mayoría de las instrucciones\Imagen con la mayoria de las instrucciones.md`

Además, he recopilado una lista detallada de comandos de la CLI y definiciones de las instrucciones del Dockerfile en el siguiente archivo:
- **[Notas importantes de Docker.txt](./Notas%20importantes%20de%20Docker.txt)**
  - *Ruta:* `Notas importantes de Docker.txt`

---

### 🚀 Práctica: Servidor Apache con Página Personalizada
Se creó una imagen personalizada basada en Ubuntu con un servidor Apache que sirve una página web estática. Se implementó un archivo `.dockerignore` para optimizar la construcción, se configuraron variables de entorno (`ENV`) para generar contenido dinámico, y se gestionaron permisos de usuario (`USER`), directorios de trabajo (`WORKDIR`) y volúmenes (`VOLUME`) para logs.

---

### 🚀 Práctica: Servidor Nginx con Múltiples Instrucciones
Se utilizó una imagen de `debian:latest` para construir un servidor Nginx, integrando la mayoría de las instrucciones de Dockerfile. Se aplicaron buenas prácticas como la creación de un usuario no-root (`USER alex`) para mejorar la seguridad, configuración de variables de entorno y manejo de permisos en la transferencia de archivos.

---

### 💡 Lecciones Aprendidas y Tips

- **Uso de `.dockerignore`**: Es una práctica fundamental para evitar enviar archivos innecesarios (como dependencias locales o archivos temporales) al contexto de construcción de la imagen, reduciendo el tamaño y mejorando la seguridad.
- **Seguridad con `USER`**: Evitar ejecutar contenedores como `root` siempre que sea posible. Crear un usuario de sistema y cambiar a él con `USER` es vital para el principio de menor privilegio.
- **Optimización de Capas**: Cada instrucción `RUN`, `COPY` y `ADD` crea una nueva capa. Es recomendable agrupar comandos (por ejemplo, actualizando e instalando en un solo `RUN`) para mantener las imágenes ligeras.
- **Diferencia entre `CMD` y `RUN`**: `RUN` se ejecuta durante el proceso de construcción de la imagen para preparar el entorno, mientras que `CMD` especifica el comando por defecto que se ejecutará cuando el contenedor inicie.

---

## Módulo 2: Trabajando con contenedores

En este módulo, el enfoque fue totalmente práctico, trabajando directamente con imágenes oficiales del Docker Hub para desplegar servicios comunes y aprender a interactuar con ellos desde la CLI.

### 📄 Recursos
El desarrollo detallado de los pasos seguidos, comandos ejecutados y resultados de estas prácticas se especifica en:
- **[Accediendo a contenedores con exec.md](./2-Trabajando%20con%20contenedores/Accediendo%20a%20contenedores%20con%20exec.md)**
  - *Ruta:* `2-Trabajando con contenedores\Accediendo a contenedores con exec.md`

---

### 🚀 Práctica: Servidor Apache (Imagen Oficial `httpd`)
Uso de la imagen oficial de Apache para comprender el flujo de descarga automática de imágenes (*pulling*) y la exploración del sistema de archivos interno del contenedor interactuando con su terminal.

---

### 🚀 Práctica: Base de Datos MySQL
Despliegue y configuración de un contenedor de MySQL utilizando variables de entorno (`-e`) para definir credenciales y verificar la creación automática de bases de datos desde la CLI.

---

### 🚀 Práctica: Base de Datos MongoDB
Uso de bases de datos NoSQL con MongoDB y acceso interactivo a la consola mediante la herramienta moderna `mongosh` para interactuar con la base de datos.

---

### 🚀 Práctica: Administración de Usuarios (Dockerfile propio)
Creación de una imagen personalizada basada en Ubuntu para configurar un usuario no-root por defecto, aprendiendo a iniciar sesiones interactivas y a alternar con privilegios de administrador (`-u root`) desde la terminal del host.

---

### 🚀 Práctica: Limitación de Recursos
Configuración de restricciones físicas en contenedores mediante límites de hardware (memoria) con el flag `-m`, y validación del consumo del sistema en tiempo real utilizando el comando `docker stats`.

---

### 💡 Lecciones Aprendidas y Tips

- **Documentación en Docker Hub**: Es vital revisar la sección "How to use this image" de cada imagen oficial, ya que ahí se especifican las variables de entorno necesarias (como en MySQL) y los puertos por defecto.
- **Persistencia**: Aunque en estas prácticas los datos son efímeros, en entornos reales debemos usar **Volúmenes** para que la información de las bases de datos no se pierda al eliminar el contenedor.
- **Interactividad**: El flag `-it` en `docker exec` es nuestra puerta de entrada para depurar y administrar servicios "desde adentro".
- **Aislamiento y Seguridad**: Limitar los recursos asignados a los contenedores (CPU, memoria) y usar usuarios no-root son prácticas de producción fundamentales para garantizar la estabilidad e integridad del sistema host.

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

## Módulo 4: Redes en Docker

En este módulo exploré cómo Docker gestiona la comunicación entre contenedores y el aislamiento de red, aprendiendo a crear redes personalizadas y a configurar la conectividad de forma precisa.

### 📄 Recursos
El desarrollo detallado de los pasos seguidos, comandos ejecutados y resultados de estas prácticas se especifica en:
- **[Redes en docker.md](./4-Redes%20en%20Docker/Redes%20en%20docker.md)**

---

### 🚀 Práctica: Redes Personalizadas y DNS
Se crearon redes con el driver `bridge` definiendo subredes y gateways específicos. Se comprobó que, a diferencia de la red bridge por defecto, las redes creadas por el usuario permiten la resolución DNS automática, permitiendo que los contenedores se comuniquen por su nombre en lugar de su IP.

### 🚀 Práctica: Conectividad y Aislamiento
Se realizaron pruebas de "ping" entre múltiples contenedores para validar:
- La comunicación fluida dentro de una misma red personalizada.
- El aislamiento total entre contenedores que pertenecen a redes distintas.
- La capacidad de conectar un contenedor a varias redes simultáneamente para actuar como puente.

### 🚀 Práctica: Redes Especiales (None y Host)
Se experimentó con los modos de red avanzados:
- **None**: Aislamiento total del contenedor sin interfaces de red externas.
- **Host**: Eliminación del aislamiento de red para que el contenedor comparta directamente la IP y puertos de la máquina host.

---

### 💡 Lecciones Aprendidas y Tips

- **DNS Interno**: Siempre es preferible crear redes personalizadas, ya que la red `bridge` por defecto no resuelve nombres de contenedores.
- **Aislamiento por Diseño**: Docker garantiza que los contenedores en redes diferentes no puedan verse entre sí, lo cual es fundamental para la seguridad en microservicios.
- **Orden de Conexión**: Si un contenedor se crea y luego se conecta a una red, mantendrá la conexión a la red original (normalmente bridge) y a la nueva, teniendo múltiples IPs.
- **Control de IPs**: El uso del flag `--ip` permite asignar direcciones estáticas dentro de nuestras subredes, algo vital para servicios que requieren configuraciones fijas.
- **Higiene de Redes**: Al igual que con los volúmenes, es buena práctica usar `docker network prune` para eliminar redes que ya no se utilizan y evitar conflictos de subredes en el futuro.

---

## Módulo 5: Docker Compose

En este módulo aprendí a utilizar Docker Compose para orquestar aplicaciones multi-contenedor, permitiendo definir toda la infraestructura (servicios, redes y volúmenes) en un único archivo declarativo.

### 📄 Recursos
El detalle de los comandos y las configuraciones de cada práctica se encuentra en:
- **[Docker Compose.md](./5-Docker%20Compose/Docker%20Compose.md)**

---

### 🚀 Práctica: Orquestación de Servicios
Se trabajó en la creación de archivos `docker-compose.yml` para automatizar el despliegue de entornos complejos.

**Puntos clave:**
- **Gestión del Ciclo de Vida**: Uso de `docker compose up -d` para levantar servicios y `down` para limpiar el entorno (contenedores y redes) de forma atómica.
- **Variables de Entorno**: Implementación de archivos `.env` (como `common.env`) para separar la configuración del código.
- **Persistencia y Redes**: Configuración de volúmenes nombrados y redes personalizadas directamente en el YAML para asegurar la persistencia de datos y la comunicación DNS entre servicios.
- **Políticas de Reinicio**: Uso de `restart: always` y `unless-stopped` para mejorar la disponibilidad de los servicios.

---

### 💡 Lecciones Aprendidas y Tips

- **Definición Declarativa**: Compose elimina la necesidad de recordar comandos largos de `docker run`; todo queda documentado en el código.
- **DNS por Servicio**: Aprendí que Compose permite que un contenedor encuentre a otro simplemente usando el nombre del servicio definido en el archivo.
- **Portabilidad**: Un proyecto con Docker Compose es fácilmente replicable en cualquier máquina, garantizando que el entorno de desarrollo sea idéntico al de otros colaboradores.
- **Limpieza Total**: El uso de `docker compose down -v` es fundamental cuando se desea resetear el entorno eliminando incluso la persistencia de datos.
- **Precedencia de Flags**: Recordar que el flag `-f` (archivo personalizado) va antes del comando, mientras que `-d` (segundo plano) va después de `up`.

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
