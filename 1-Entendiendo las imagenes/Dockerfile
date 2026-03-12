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

#Con CMD, especificamos el comando que se ejecutará cuando se inicie un contenedor a partir de la imagen.
#En este caso, se ejecutará el comando "apache2ctl -D FOREGROUND" que inicia el servidor web Apache en primer plano. 
    #Esto es importante porque si el proceso principal del contenedor termina, el contenedor se detendrá. 
    #Al mantener Apache en primer plano, aseguramos que el contenedor siga funcionando mientras el servidor web esté activo.
    #Al colocar la instruccion en corchetes y comillas, se ejecuta el comando directamente sin pasar por un shell, lo que hace que apache se ejecute como el proceso principal del contenedor.
CMD ["apache2ctl", "-D", "FOREGROUND"]
