# Use a imagem PHP 8.1 FPM
FROM php:8.1-fpm

# Mude o diretório de trabalho para o projeto Laravel
WORKDIR /var/www/project-laravel

# Instale as dependências necessárias
RUN apt-get update && apt-get upgrade -y \
    && apt-get install -y \
        build-essential \
        libpng-dev \
        libwebp-dev \
        libjpeg62-turbo-dev \
        libfreetype6-dev \
        locales \
        zip \
        jpegoptim optipng pngquant gifsicle \
        vim \
        unzip \
        git \
        curl \
        libzip-dev \
        wget

# Instale as extensões do PHP
RUN docker-php-ext-install pdo_mysql zip exif pcntl \
    && docker-php-ext-configure gd --with-jpeg --with-webp --with-freetype \
    && docker-php-ext-install -j$(nproc) gd

# Limpeza do cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Instale o Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Adicione um usuário para a aplicação Laravel
RUN groupadd -g 1000 www && useradd -u 1000 -ms /bin/bash -g www www

# Copie o composer.json para o diretório /var/www/
COPY composer.json /var/www/

# Copie os arquivos do projeto para o diretório de trabalho
COPY . /var/www/project-laravel

# Copie o arquivo de configuração PHP local
COPY php/local.ini /usr/local/etc/php/conf.d/local.ini

# Copie o arquivo de configuração MySQL
COPY mysql/my.cnf /etc/mysql/my.cnf

# PhpMyAdmin instalado na porta 80
RUN wget https://files.phpmyadmin.net/phpMyAdmin/5.1.1/phpMyAdmin-5.1.1-all-languages.zip -O /tmp/phpmyadmin.zip \
    && unzip /tmp/phpmyadmin.zip -d /var/www/html/ \
    && mv /var/www/html/phpMyAdmin-5.1.1-all-languages /var/www/html/phpmyadmin \
    && chown -R www:www /var/www/html/phpmyadmin

# Exponha a porta 9000 e inicia o servidor Php-fpm
EXPOSE 9000
CMD ["php-fpm"]
