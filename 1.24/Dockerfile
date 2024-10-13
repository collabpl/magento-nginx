FROM nginx:1.24-alpine

ARG APP_ID=1000
ARG MAGENTO_ROOT=/var/www/html

RUN addgroup -g "$APP_ID" www \
 && adduser -G www -u "$APP_ID" -h /var/www -s /bin/bash -S www
RUN touch /var/run/nginx.pid
RUN mkdir /sock

RUN mkdir -p ${MAGENTO_ROOT} \
  && chown -R www:www /etc/nginx ${MAGENTO_ROOT} /var/cache/nginx /var/run/nginx.pid /sock

COPY conf/nginx.conf /etc/nginx/
RUN mkdir -p ${MAGENTO_ROOT}

EXPOSE 80

USER www:www

WORKDIR /var/www/html
