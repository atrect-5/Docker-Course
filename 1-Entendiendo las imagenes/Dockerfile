#Con FROM, definimos la imagen base. Es la imagen a partir de la cual se va a construir la nueva imagen. Es obligatorio definirla.
#En este caso, se está utilizando la imagen oficial de Ubuntu 20.04 como base para construir la nueva imagen.
FROM ubuntu:20.04

# Con ENV, definimos variables de entorno que estarán disponibles durante el proceso de construcción de la imagen y también en los contenedores que se creen a partir de esta imagen.
# Evitamos que la instalación pida interacción y configuramos la zona horaria de México
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=America/Mexico_City

#Con RUN, ejecutamos un comando en la terminal durante el proceso de construcción de la imagen. 
#En este caso, actualizamos los repositorios e instalamos el servidor web Apache.
RUN apt-get update && apt-get install -y apache2