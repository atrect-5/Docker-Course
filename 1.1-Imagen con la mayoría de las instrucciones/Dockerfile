# Dockerfile para configurar un servidor Nginx en Debian, con un usuario específico y archivos personalizados.
# El contenedor se ejecutará con Nginx en primer plano y expondrá el puerto 80 para servir contenido web.

# Este Dockerfile crea una imagen usando las instrucciones vistas en el curso de Fundamentos de Docker

FROM debian:latest

LABEL vendor="Alejandro Gonzalez"

RUN apt-get update && apt-get install --no-install-recommends -y nginx; \
 echo "daemon off;" >> /etc/nginx/nginx.conf

RUN useradd alex

COPY index.html /var/www/html

ENV file=data

WORKDIR /var/www/html

RUN echo "$file" > ./envi.html

USER alex

RUN echo "I am $(whoami) and I am creating this file" > /tmp/username.html

USER root

RUN cp /tmp/username.html ./username.html

VOLUME /var/log/nginx

EXPOSE 80

CMD ["/usr/sbin/nginx"]
