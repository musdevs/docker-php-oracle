FROM php:7.3-cli

RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
    	libjpeg62-turbo-dev \
    	libpng-dev \
    	git \
    	libzip-dev \
        zip \
        unzip \
        libc-bin \
        libaio-dev \
	&& docker-php-ext-install -j$(nproc) iconv \
	&& docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install -j$(nproc) bcmath \
    && docker-php-ext-configure zip --with-libzip \
    && docker-php-ext-install zip

# ************** pdo_pgsql **************

RUN apt-get install -y libpq-dev \
    && docker-php-ext-configure pgsql --with-pgsql=/usr/local/pgsql \
    && docker-php-ext-install pdo_pgsql

# ************** XDebug **************

RUN yes | pecl install xdebug-2.7.2 \
    && docker-php-ext-enable xdebug \
    && echo "max_execution_time=1800" > $PHP_INI_DIR/conf.d/app_custom.ini

# ************** Oracle OCI extension **************

# https://github.com/FabriZZio/docker-php-oci8/blob/master/Dockerfile

ADD oracle/instantclient-basiclite-linux.x64-19.3.0.0.0dbru.zip /tmp/instantclient-basiclite-linux.x64-19.3.0.0.0dbru.zip
ADD oracle/instantclient-sdk-linux.x64-19.3.0.0.0dbru.zip /tmp/instantclient-sdk-linux.x64-19.3.0.0.0dbru.zip

RUN unzip /tmp/instantclient-basiclite-linux.x64-19.3.0.0.0dbru.zip -d /usr/local \
    && unzip /tmp/instantclient-sdk-linux.x64-19.3.0.0.0dbru.zip -d /usr/local \
    && ln -s /usr/local/instantclient_19_3 /usr/local/instantclient

ENV LD_LIBRARY_PATH /usr/local/instantclient
ENV ORACLE_HOME /usr/local/instantclient

RUN echo 'instantclient,/usr/local/instantclient' | pecl install oci8 \
    && echo "extension=oci8.so" > $PHP_INI_DIR/conf.d/oci8.ini

# *************** Composer ***************

ENV COMPOSER_ALLOW_SUPERUSER 1
ENV COMPOSER_HOME /tmp
ENV COMPOSER_VERSION 1.8.6

RUN curl --silent --fail --location --retry 3 --output /tmp/installer.php --url https://raw.githubusercontent.com/composer/getcomposer.org/cb19f2aa3aeaa2006c0cd69a7ef011eb31463067/web/installer \
 && php -r " \
    \$signature = '48e3236262b34d30969dca3c37281b3b4bbe3221bda826ac6a9a62d6444cdb0dcd0615698a5cbe587c3f0fe57a54d8f5'; \
    \$hash = hash('sha384', file_get_contents('/tmp/installer.php')); \
    if (!hash_equals(\$signature, \$hash)) { \
      unlink('/tmp/installer.php'); \
      echo 'Integrity check failed, installer is either corrupt or worse.' . PHP_EOL; \
      exit(1); \
    }" \
 && php /tmp/installer.php --no-ansi --install-dir=/usr/bin --filename=composer --version=${COMPOSER_VERSION} \
 && composer --ansi --version --no-interaction \
 && rm -f /tmp/installer.php \
 && find /tmp -type d -exec chmod -v 1777 {} +

 WORKDIR /app
