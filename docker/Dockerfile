FROM php:8.2-apache

RUN apt-get update && apt-get install -y \
    libpq-dev \
    libonig-dev \
    libzip-dev \
    zip \
    libcurl4 \
    libcurl4-openssl-dev \ 
    unzip \
    libxml2-dev 

RUN apt-get clean && rm -rf /var/lib/apt/lists/*

RUN a2enmod rewrite

COPY ./docker/apache/apache-config.conf /etc/apache2/sites-available/000-default.conf

ENV APACHE_DOCUMENT_ROOT /var/www/html/public

RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf

RUN docker-php-ext-install pdo_mysql zip curl \
    xml 

USER root

COPY . .

RUN cp .env.example .env

WORKDIR /var/www/html

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN composer install

RUN chown -R www-data:www-data /var/www/html \
    && chmod 775 -R /var/www/html 
    
RUN chmod -R 777 /var/www/html/storage

RUN chown -R www-data:www-data /var/www/html/storage /var/www/html/bootstrap/cache

RUN chmod -R 777 ./storage/

RUN php artisan key:generate

USER www-data

# Expose port 80 for Apache.
EXPOSE 80

# Start Apache web server.
CMD ["/usr/sbin/apachectl", "-D", "FOREGROUND"]