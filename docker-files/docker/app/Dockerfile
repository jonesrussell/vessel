FROM bitnami/php-fpm:7.0.33-debian-9

LABEL maintainer="Chris Fidao"

RUN useradd -ms /bin/bash -u 1337 vessel
WORKDIR /var/www/html

ENV TZ=EDT
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN set -x \
    && apt-get update && apt-get install -y gnupg gosu \
    && gosu nobody true

RUN apt-get update \
    && apt-get install -y curl zip unzip git supervisor sqlite3 \
    && apt-get install -y nginx php7.0-fpm php7.0-cli \
       php7.0-pgsql php7.0-sqlite3 php7.0-gd \
       php7.0-curl php7.0-memcached \
       php7.0-imap php7.0-mysql php7.0-mbstring \
       php7.0-xml php7.0-zip php7.0-bcmath php7.0-soap \
       php7.0-intl php7.0-readline php7.0-xdebug \
       php7.0-msgpack php7.0-igbinary php7.0-ldap \
    && php -r "readfile('http://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer \
    && mkdir /run/php \
    && apt-get -y autoremove \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* \
    && echo "daemon off;" >> /etc/nginx/nginx.conf

RUN ln -sf /dev/stdout /var/log/nginx/access.log \
    && ln -sf /dev/stderr /var/log/nginx/error.log

COPY h5bp /etc/nginx/h5bp
COPY default /etc/nginx/sites-available/default
COPY php-fpm.conf /etc/php/7.0/fpm/php-fpm.conf
COPY xdebug.ini /etc/php/7.0/mods-available/xdebug.ini
COPY vessel.ini /etc/php/7.0/fpm/conf.d/99-vessel.ini

EXPOSE 80

COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY start-container /usr/local/bin/start-container
RUN chmod +x /usr/local/bin/start-container

ENTRYPOINT ["start-container"]
