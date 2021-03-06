FROM php:8-fpm

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        git \
        rsync \
        libbz2-dev \
        libcurl4-openssl-dev \
        libfreetype6-dev \ 
        libjpeg62-turbo-dev \
        libldap2-dev \
        libonig-dev \
        libpng-dev \
        libxml2-dev \
        libxpm-dev \
        libzip-dev

RUN docker-php-ext-configure gd --with-freetype=/usr/include/ \
                                --with-jpeg=/usr/include/ \
                                --with-xpm=/usr/include/ \
                                --enable-gd-jis-conv \
    && docker-php-ext-configure mysqli -with-mysqli=mysqlnd \
    && docker-php-ext-configure pdo_mysql -with-pdo-mysql=mysqlnd \
    && docker-php-ext-install -j$(nproc) \
        bz2 \
        curl \
        exif \
        gd \
        intl \
        ldap \
        mbstring \
        mysqli \
        opcache \
        pdo \
        pdo_mysql \
        xml \
        # xmlrpc /\
        zip \
    && pecl install apcu && docker-php-ext-enable apcu

ENV GLPI_VERSION 9.5.3

RUN git clone --progress -b "${GLPI_VERSION}" --single-branch --depth 1 https://github.com/glpi-project/glpi.git /usr/src/glpi

ENV NVM_DIR=/opt/nvm
RUN git clone https://github.com/nvm-sh/nvm.git $NVM_DIR
RUN cd $NVM_DIR && \
    git checkout `git describe --abbrev=0 --tags --match "v[0-9]*" $(git rev-list --tags --max-count=1)` && \
    . $NVM_DIR/nvm.sh && \
    nvm install 13 && \
    nvm use node
RUN echo "source ${NVM_DIR}/nvm.sh" > $HOME/.bashrc

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# RUN pecl install xdebug-3.0.2 \
#     && docker-php-ext-enable xdebug

VOLUME /var/www/html
WORKDIR /var/www/html

COPY glpi-scripts /usr/src/glpi-scripts
ENTRYPOINT ["sh", "/usr/src/glpi-scripts/entrypoint.sh"]

CMD ["php-fpm"]