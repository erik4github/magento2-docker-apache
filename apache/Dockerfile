FROM ubuntu:18.04

RUN apt-get update && apt-get install -y --no-install-recommends \
    apt-utils \
    dialog \
    wget \
    libfreetype6-dev \
    libjpeg-dev \
    libmagickwand-dev \
    libpng-dev \
    libzip-dev \
    gettext-base \
    apache2 \
    # clean up apt cache
    && rm -rf /var/lib/apt/lists/*

ARG USERNAME=app
ARG USER_UID=1000
ARG USER_GID=$USER_UID

RUN groupadd --gid $USER_GID $USERNAME \
 && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME -d /var/www \
 && usermod -a -G www-data $USERNAME

RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf
COPY ./apache/conf/vhost.conf.template /etc/apache2/sites-available/vhost.conf.template
ARG APP_HOST
RUN envsubst '${APP_HOST}' < /etc/apache2/sites-available/vhost.conf.template > /etc/apache2/sites-available/000-default.conf

RUN a2enmod expires headers rewrite proxy proxy_fcgi

EXPOSE 80

CMD ["apache2ctl", "-D", "FOREGROUND"]

VOLUME /var/www

WORKDIR /var/www/html