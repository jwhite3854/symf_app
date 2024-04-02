FROM ubuntu/apache2:2.4-22.04_edge

ARG RELEASE
ARG LAUNCHPAD_BUILD_ARCH

LABEL org.opencontainers.image.ref.name=ubuntu
LABEL org.opencontainers.image.version=22.04

ENV TZ=UTC
RUN apt-get update && apt-get install -y apt-utils && apt-get install -y software-properties-common
RUN DEBIAN_FRONTEND=noninteractive apt-get full-upgrade -y;  \
    rm -rf /var/lib/apt/lists/*

RUN DEBIAN_FRONTEND=noninteractive apt-get update; \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends apache2 curl tzdata wget git vim; \
    DEBIAN_FRONTEND=noninteractive apt-get install -y gcc;  \
    DEBIAN_FRONTEND=noninteractive apt-get remove --purge --auto-remove -y;  \
    rm -rf /var/lib/apt/lists/*;  \
    apache2 -v;

RUN sed -i 's|^ErrorLog .*$|ErrorLog /dev/stderr|' /etc/apache2/apache2.conf; 	 \
    sed -i '/^ErrorLog /a TransferLog /dev/stdout' /etc/apache2/apache2.conf; 	 \
    sed -i -e '/ErrorLog /d' -e '/CustomLog /d' /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/default-ssl.conf

COPY ./vhost.conf /etc/apache2/sites-available/local.vhost.conf
RUN mkdir /var/www/vhosts/vetl.localhost/app/public/ -p
RUN mkdir /var/www/vhosts/vetl.localhost/logs/

RUN C_ALL=C.UTF-8 add-apt-repository -y ppa:ondrej/php && apt-get update
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y php8.3 php-pear
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y php8.3-curl

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && php composer-setup.php
RUN php -r "unlink('composer-setup.php');" && mv composer.phar /usr/local/bin/composer

EXPOSE 80

RUN a2ensite local.vhost
RUN a2dissite 000-default

RUN wget https://get.symfony.com/cli/installer -O - | bash
RUN mv /root/.symfony5/bin/symfony /usr/local/bin/symfony

CMD ["apache2-foreground"]