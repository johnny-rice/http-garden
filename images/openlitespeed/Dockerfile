FROM http-garden-soil:latest

WORKDIR /app

RUN apt -y update && apt -y install --no-install-recommends php-fpm libudns-dev

ARG APP_REPO
RUN git clone --recurse-submodules "$APP_REPO"

ARG APP_VERSION
RUN cd /app/openlitespeed && git checkout "$APP_VERSION" && ./build.sh && ./install.sh

RUN rm -rf /usr/local/lsws/Example && mkdir /usr/local/lsws/the_vhost
COPY echo.php /usr/local/lsws/the_vhost/echo.php

RUN cp /usr/local/lsws/conf/mime.properties /tmp && rm -rf /usr/local/lsws/conf && mkdir /usr/local/lsws/conf && mv /tmp/mime.properties /usr/local/lsws/conf/mime.properties

ARG CONFIG_FILE
COPY $CONFIG_FILE /usr/local/lsws/conf/httpd_config.conf

ARG VHOST_CONFIG_FILE
COPY $VHOST_CONFIG_FILE /usr/local/lsws/conf/the_vhost.conf

ARG BACKEND
RUN sed -i "s/PROXY_BACKEND_PLACEHOLDER/$BACKEND/g" /usr/local/lsws/conf/httpd_config.conf

RUN sed -i 's/^pm\.max_children = 5$/pm.max_children = 500/' /etc/php/8.2/fpm/pool.d/www.conf

CMD php-fpm8.2 && chown nobody:nogroup /run/php/php8.2-fpm.sock && /usr/local/lsws/bin/litespeed && sleep infinity
