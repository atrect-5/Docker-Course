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

# Con WORKDIR, establecemos el directorio de trabajo para las instrucciones RUN, CMD, ENTRYPOINT, COPY y ADD que sigan en el Dockerfile.
# Si el directorio no existe, se creará. Esto ayuda a mantener el Dockerfile más limpio y legible.
WORKDIR /var/www/html

# Definimos una variable de entorno que se usará para generar una nueva página.
ENV SALUDO="Hola, esta es una página de prueba para entender las varibles de entorno en Docker."
# Creamos un archivo HTML ('variable.html') utilizando el contenido de la variable de entorno SALUDO.
RUN echo $SALUDO > variable.html

#Con COPY, copiamos archivos o directorios desde el contexto de construcción (nuestra máquina) al sistema de archivos de la imagen.
#Aquí, copiamos nuestra página de prueba al directorio por defecto de Apache en Ubuntu.
COPY index.html .

# Con EXPOSE, informamos a Docker que el contenedor escuchará en los puertos de red especificados en tiempo de ejecución.
# Esta instrucción no publica el puerto, solo funciona como un tipo de documentación entre la persona que construye la imagen y la persona que ejecuta el contenedor.
# Apache escucha en el puerto 80 por defecto.
EXPOSE 80

#Con CMD, especificamos el comando que se ejecutará cuando se inicie un contenedor a partir de la imagen.
#En este caso, se ejecutará el comando "apache2ctl -D FOREGROUND" que inicia el servidor web Apache en primer plano. 
    #Esto es importante porque si el proceso principal del contenedor termina, el contenedor se detendrá. 
    #Al mantener Apache en primer plano, aseguramos que el contenedor siga funcionando mientras el servidor web esté activo.
    #Al colocar la instruccion en corchetes y comillas, se ejecuta el comando directamente sin pasar por un shell, lo que hace que apache se ejecute como el proceso principal del contenedor.
CMD ["apache2ctl", "-D", "FOREGROUND"]
