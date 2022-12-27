FROM nginx:stable-alpine

ARG NGINXGROUP
ARG NGINXUSER

ENV NGINXGROUP=${NGINXGROUP}
ENV NGINXUSER=${NGINXUSER}

RUN sed -i "s/user www-data/user ${NGINXUSER}/g" /etc/nginx/nginx.conf

ADD default.conf /etc/nginx/conf.d/default.conf
#COPY default.conf /etc/nginx/conf.d/default.conf

RUN mkdir -p /var/www/html

RUN adduser -g ${NGINXGROUP} -s /bin/sh -D ${NGINXUSER}; exit 0