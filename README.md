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

### 💻 Práctica Realizada
En la carpeta `1-Entendiendo las imagenes`, creé mi primer `Dockerfile` para configurar un entorno base.

**Instrucciones utilizadas:**
- `FROM`: Para definir la imagen base (Ubuntu 20.04).
- `ENV`: Para configurar variables de entorno (Zona horaria y modo no interactivo para evitar bloqueos en la instalación).
- `RUN`: Para ejecutar comandos de instalación (Apache2).

### 📄 Recursos
He recopilado una lista de comandos útiles y definiciones detalladas en el archivo:
- Notas importantes de Docker.txt
