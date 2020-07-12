FROM nginx:1.17-alpine

LABEL maintainer="Mahmoud Zalt <mahmoud@zalt.me>"

ADD nginx.conf /etc/nginx/

RUN apk update \
    && apk upgrade \
    && apk add --no-cache bash \
    && adduser -D -H -u 1000 -s /bin/bash www-data -G www-data

ARG NGINX_PHP_UPSTREAM_PORT=9000

# Apps
RUN echo "upstream challenges-upstream { server challenges:${NGINX_PHP_UPSTREAM_PORT}; }" > /etc/nginx/conf.d/upstream.conf

COPY sites/ /etc/nginx/sites-available/
RUN rm /etc/nginx/conf.d/default.conf

CMD ["nginx"]

EXPOSE 80 443
