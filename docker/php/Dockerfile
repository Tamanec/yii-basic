FROM php:fpm

MAINTAINER Denis Sidorov <denis.sidorov@altarix.ru>

RUN apt-get -q update && \
    apt-get -y install \
            g++ \
            git \
            libicu-dev \
            libmcrypt-dev \
            libfreetype6-dev \
            libjpeg-dev \
            libjpeg62-turbo-dev \
            libmcrypt-dev \
            libpng12-dev \
            zlib1g-dev \
            mysql-client \
            libmysqlclient-dev \
            openssh-client \
            libxml2-dev \
            nano \
            vim \
            zip \
            unzip \
        --no-install-recommends && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Install PHP extensions required for Yii 2.0 Framework
RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-png-dir=/usr/include/ && \
    docker-php-ext-configure bcmath && \
    docker-php-ext-configure pdo_mysql --with-pdo-mysql=mysqlnd && \
    docker-php-ext-install gd \
                           intl \
                           pdo_mysql \
                           mbstring \
                           mcrypt \
                           zip \
                           bcmath \
                           exif \
                           pcntl \
                           soap && \
    docker-php-ext-enable opcache

RUN yes | pecl install xdebug && \
    docker-php-ext-enable xdebug

ENV VERSION_COMPOSER_ASSET_PLUGIN=^1.2.0 \
    VERSION_PRESTISSIMO_PLUGIN=^0.3.0 \
    PATH=/app:/app/vendor/bin:/root/.composer/vendor/bin:$PATH \
    TERM=linux \
    COMPOSER_ALLOW_SUPERUSER=1 \
    COMPOSER_GITHUB_OAUTH="b05a6a826691f0c074aa5a8a383b04af6883f584" \
    VERSION_CODECEPTION=^2.2.6 \
    VERSION_YII2=^2.0.10

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- \
        --filename=composer \
        --install-dir=/usr/local/bin && \
    composer global require --optimize-autoloader \
        "fxp/composer-asset-plugin:${VERSION_COMPOSER_ASSET_PLUGIN}" \
        "hirak/prestissimo:${VERSION_PRESTISSIMO_PLUGIN}" && \
    composer config -g github-oauth.github.com ${COMPOSER_GITHUB_OAUTH} && \
    composer global dumpautoload --optimize

# Install CLI packages required for Yii 2.0 Framework globally
RUN composer global require  --prefer-dist \
        "codeception/codeception:${VERSION_CODECEPTION}" \
        "codeception/specify:*" \
        "codeception/verify:*" && \
    composer global dumpautoload --optimize

# Install Yii 2.0 Framework libraries globally (for build caching)
RUN composer global require  --prefer-dist \
        "yiisoft/yii2:${VERSION_YII2}" \
        "yiisoft/yii2-bootstrap" \
        "yiisoft/yii2-codeception" \
        "yiisoft/yii2-gii" \
        "yiisoft/yii2-debug" && \
    composer global dumpautoload --optimize

ADD php.ini /usr/local/etc/php/php.ini
