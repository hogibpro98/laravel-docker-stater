FROM php:7.4-fpm-bullseye

RUN addgroup --gid 1000 docker && \
    adduser --uid 1000 --ingroup docker --home /home/docker --shell /bin/sh --disabled-password --gecos "" docker
RUN USER=docker && \
    GROUP=docker && \
    curl -SsL https://github.com/boxboat/fixuid/releases/download/v0.5.1/fixuid-0.5.1-linux-amd64.tar.gz | tar -C /usr/local/bin -xzf - && \
    chown root:root /usr/local/bin/fixuid && \
    chmod 4755 /usr/local/bin/fixuid && \
    mkdir -p /etc/fixuid && \
    printf "user: $USER\ngroup: $GROUP\n" > /etc/fixuid/config.yml

WORKDIR /var/www

COPY start.sh /scripts/start.sh
RUN chmod +x /scripts/start.sh

RUN apt-get --quiet --assume-yes update

RUN apt-get --quiet --assume-yes --verbose-versions install -y ca-certificates curl pkg-config zlib1g-dev libpng-dev libjpeg-dev libfreetype6-dev libcurl4-openssl-dev build-essential libicu-dev libonig-dev libxml2-dev libzip-dev

COPY trust-ca.crt /usr/local/share/ca-certificates/
RUN update-ca-certificates

RUN docker-php-ext-configure gd --with-freetype --with-jpeg
RUN docker-php-ext-install \
    bcmath \
    calendar \
    curl \
    exif \
    gd \
    intl \
    mbstring \
    pdo \
    pdo_mysql \
    pcntl \
    tokenizer \
    xml \
    zip

COPY --from=mlocati/php-extension-installer:1.5.12 /usr/bin/install-php-extensions /usr/local/bin/

RUN pecl install xdebug-3.1.0 \
    && docker-php-ext-enable xdebug
COPY docker-php-ext-xdebug.ini /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

COPY php-fpm.conf /usr/local/etc/php-fpm.conf
COPY www.conf /usr/local/etc/php-fpm.d/www.conf
COPY php.ini /usr/local/etc/php/php.ini

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN mkdir -p /var/www/html

WORKDIR /var/www/html

USER docker:docker
ENTRYPOINT ["fixuid"]

CMD ["/scripts/start.sh"]
