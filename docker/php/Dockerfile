FROM php:8.1-fpm

ARG PHPGROUP
ARG PHPUSER

ENV PHPGROUP=${PHPGROUP}
ENV PHPUSER=${PHPUSER}

RUN adduser -g ${PHPGROUP} -s /bin/sh -D ${PHPUSER}; exit 0

RUN mkdir -p /var/www/html

WORKDIR /var/www/html

RUN sed -i "s/user = www-data/user = ${PHPUSER}/g" /usr/local/etc/php-fpm.d/www.conf
RUN sed -i "s/group = www-data/group = ${PHPGROUP}/g" /usr/local/etc/php-fpm.d/www.conf

RUN apt-get update && apt-get install -y \
    build-essential \
    libpng-dev \
    libonig-dev \
    libjpeg62-turbo-dev \
    libfreetype6-dev \
    locales \
    zip \
    jpegoptim optipng pngquant gifsicle \
    vim \
    unzip \
    git \
    nano \
    curl \
    zlib1g-dev \
    libzip-dev \
    sendmail \
    && docker-php-ext-configure gd --with-freetype --with-jpeg

#RUN apt-get update \
#    && apt-get install -y gconf-service libasound2 libappindicator3-1 libatk1.0-0 libc6 #libcairo2 libcups2 libdbus-1-3 libexpat1 libfontconfig1 libgbm1 libgcc1 libgconf-2-4 #libgdk-pixbuf2.0-0 libglib2.0-0 libgtk-3-0 libnspr4 libpango-1.0-0 libpangocairo-1.0-0 #libstdc++6 libx11-6 libx11-xcb1 libxcb1 libxcomposite1 libxcursor1 libxdamage1 libxext6 #libxfixes3 libxi6 libxrandr2 libxrender1 libxss1 libxtst6 ca-certificates #fonts-liberation libappindicator1 libnss3 lsb-release xdg-utils wget libgbm-dev #libatk-bridge2.0-0 \
#    && npm install --global --unsafe-perm puppeteer \
#    && chmod -R o+rx /usr/lib/node_modules/puppeteer/.local-chromium


RUN docker-php-ext-install pdo pdo_mysql zip -j$(nproc) gd mbstring exif pcntl
# RUN docker-php-ext-enable sodium bz2

# Install wkhtmltopdf
RUN apt-get update && apt-get -y install wget
RUN wget https://github.com/wkhtmltopdf/packaging/releases/download/0.12.6-1/wkhtmltox_0.12.6-1.buster_amd64.deb
RUN apt install ./wkhtmltox_0.12.6-1.buster_amd64.deb -y
RUN rm wkhtmltox_0.12.6-1.buster_amd64.deb

# install sendmail 
RUN echo "sendmail_path=/usr/sbin/sendmail -t -i" >> /usr/local/etc/php/conf.d/sendmail.ini 
# SEND MAIL SET UP
RUN sed -i '/#!\/bin\/sh/aservice sendmail restart' /usr/local/bin/docker-php-entrypoint
RUN sed -i '/#!\/bin\/sh/aecho "$(hostname -i)\t$(hostname) $(hostname).localhost" >> /etc/hosts' /usr/local/bin/docker-php-entrypoint

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

#SET UP REDIS
RUN mkdir -p /usr/src/php/ext/redis \
    && curl -L https://github.com/phpredis/phpredis/archive/5.3.4.tar.gz | tar xvz -C /usr/src/php/ext/redis --strip 1 \
    && echo 'redis' >> /usr/src/php-available-exts \
    && docker-php-ext-install redis

# And clean up the image
RUN apt-get clean && rm -rf /var/lib/apt/lists/*
#RUN rm -rf /var/lib/apt/lists/*

# Expose port 9000 and start php-fpm server
EXPOSE 9000
CMD ["php-fpm", "-y", "/usr/local/etc/php-fpm.conf", "-R"]
