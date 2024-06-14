# Partimos de la imagen php en su versión 8.2
FROM php:8.2-fpm

# Nos movemos a /var/www/
WORKDIR /var/www/

# Copiamos los archivos composer.json y composer.lock a /var/www/
COPY composer*.json /var/www/

# Instalamos las dependencias necesarias
RUN apt-get update && apt-get install -y \
    build-essential \
    libzip-dev \
    libpng-dev \
    libjpeg62-turbo-dev \
    libfreetype6-dev \
    libonig-dev \
    locales \
    zip \
    jpegoptim optipng pngquant gifsicle \
    vim \
    git \
    curl

# Instalamos extensiones de PHP
RUN docker-php-ext-install pdo_mysql zip exif pcntl
RUN docker-php-ext-configure gd --with-freetype --with-jpeg
RUN docker-php-ext-install gd

# Instalamos Composer
RUN curl -sS "https://getcomposer.org/installer" | php -- --install-dir=/usr/local/bin --filename=composer

# Permitir que Composer se ejecute como superusuario (opcional)
ENV COMPOSER_ALLOW_SUPERUSER 1

# Copiamos todos los archivos del proyecto al contenedor
COPY . /var/www/

# Nos aseguramos de estar en el directorio /var/www/ y ejecutamos composer install
RUN composer install --no-ansi --no-dev --no-interaction --no-progress --optimize-autoloader --no-scripts

# Damos permisos a las carpetas storage y bootstrap/cache
RUN chown -R www-data:www-data /var/www/storage /var/www/bootstrap/cache
RUN chmod -R 777 /var/www/storage /var/www/bootstrap/cache

# Ejecutar comandos de Artisan después de que Composer haya instalado todas las dependencias
RUN php /var/www/artisan key:generate
RUN php /var/www/artisan migrate --force

# Exponemos el puerto 9000 a la network
EXPOSE 9000

# Corremos el comando php-fpm para ejecutar PHP
CMD ["php-fpm"]
