# Curso Fundamentos de Docker - Azul School 🐳

Bienvenido a mi repositorio de notas y prácticas del curso de Docker. Aquí iré documentando los conceptos fundamentales y comandos que vaya aprendiendo.

> **Estado:** 🚧 En construcción / En curso

## 📖 Tabla de Contenidos

1. [Arquitectura de Docker](#arquitectura-de-docker)
2. [Imágenes](#imágenes)
3. [Contenedores](#contenedores)

---

## Arquitectura de Docker

Docker utiliza una arquitectura **cliente-servidor** que permite flexibilidad en el despliegue.

*   **Docker Daemon:** El servicio en segundo plano que hace el trabajo pesado (construir, ejecutar, distribuir).
*   **Docker Client:** La interfaz de línea de comandos (CLI) con la que interactuamos.
*   **Docker Registries:** Repositorios centralizados (como Docker Hub) para descargar y subir imágenes.

**Flujo General:**
`Usuario` → `Cliente` → `Daemon` → `Gestión de Contenedores`

## Imágenes

Una imagen es una **plantilla inmutable** que contiene todo lo necesario para ejecutar una aplicación (código, bibliotecas, dependencias, etc.).

*   Se construyen a partir de un archivo de texto llamado `Dockerfile`.
*   Están compuestas por **capas** para optimizar el almacenamiento y la reutilización.

## Contenedores

Un contenedor es una **instancia en ejecución** de una imagen.

*   **Aislamiento:** Cada contenedor tiene su propio sistema de archivos y red, aunque comparten el kernel del sistema operativo.
*   **Efímeros:** Son volátiles; se pueden crear, detener y eliminar fácilmente, lo que los hace ideales para escalar aplicaciones.
