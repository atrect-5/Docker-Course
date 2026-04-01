# Se utilizara la ultima imagen de ubuntu como base.
FROM ubuntu:latest

ENV prueba="Esta es una prueba del uso de usuarios"

# Se creara un usuario de prueba para cuando se ejecute la consola del contenedor basado en esta imagen
RUN useradd usuario-prueba

# Se selecciona el usuario de prueba que creamos
USER usuario-prueba