FROM php:7.2-apache
LABEL maintainer="mentrena@aicor.com"

# Install the required packages and remove the apt cache.
RUN apt-get update -yqq \
    && apt-get install -yqq --no-install-recommends \
    git \
    zip \
    unzip \
    sudo \
    make \
    libmcrypt-dev \
    libzip-dev \
    openssh-client \
    && rm -rf /var/lib/apt/lists

# Install mcrypt
RUN pecl install mcrypt-1.0.4 \
    && docker-php-ext-enable mcrypt

# Añadir configuración de Xdebug 2
RUN pecl install -o -f xdebug-2.9.0 && docker-php-ext-enable xdebug
COPY docker/xdebug.ini /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

# Habilitar mod_rewrite de Apache (opcional, si lo necesitas)
RUN a2enmod rewrite

# Enable PHP extensions
RUN docker-php-ext-install pdo_mysql mysqli

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin/ --filename=composer

# Add cake command to system path
ENV PATH="${PATH}:/var/www/html/lib/Cake/Console"
ENV PATH="${PATH}:/var/www/html/app/Vendor/bin"

# COPY site conf file
COPY ./docker/apache/site.conf /etc/apache2/sites-available/000-default.conf

# Copy the code into /var/www/html/ inside the image
COPY . .

# Enable Apache modules and restart
RUN a2enmod rewrite \
    && service apache2 restart

# Crear usuario del contenedor
# RUN groupadd -g 1000 entrenadev && useradd -m -s /bin/bash -u 1000 -g entrenadev entrenadev



# Set default working directory
# WORKDIR /var/www/html/app

RUN groupmod -g 1000 www-data \
    && usermod -u 1000 www-data

# Cambia el propietario y los permisos de /var/www
RUN chown -R www-data:www-data /var/www

# Create tmp directory and make it writable by the web server
RUN mkdir -p \
    tmp/cache/models \
    tmp/cache/persistent \
    && chown -R www-data:www-data \
    tmp \
    && chmod -R 770 \
    tmp

# Set default working directory
# WORKDIR /var/www/html

# RUN chown -R www-data:www-data lib/Cake/Cache \
#     && chmod -R 770 lib/Cake/Cache

# Usuario nuevo se usa
USER www-data

# Set default working directory
WORKDIR /var/www/html/app

EXPOSE 80
