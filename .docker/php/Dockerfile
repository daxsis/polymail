# # # Build arguments
# # ARG IMAGE_REGISTRY=dev
# # ARG IMAGE_TAG=latest

# # # Base image
# # FROM ${IMAGE_REGISTRY}/api-base:${IMAGE_TAG} as base

# FROM php:8.1-fpm

# WORKDIR /var/www/html

# RUN apt-get update && apt-get install --quiet --yes --no-install-recommends \
# 	libzip-dev \
# 	unzip\
# 	libonig-dev \
# 	libicu-dev

# RUN docker-php-ext-configure pcntl --enable-pcntl \
# 	&& docker-php-ext-configure pdo_mysql --with-pdo-mysql \
# 	&& docker-php-ext-configure mbstring --enable-mbstring \
# 	&& docker-php-ext-configure bcmath --enable-bcmath \
# 	# GD
# 	apt-get install -y libfreetype6-dev libjpeg62-turbo-dev libpng-dev \
# 	# Intl
# 	&& apt-get install -y zlib1g-dev libicu-dev g++ \
#     && docker-php-ext-configure intl \
#     && docker-php-ext-configure gd --with-jpeg=/usr/include/ --with-freetype=/usr/include/ \
# 	&& docker-php-ext-install gd \
# 	intl \
# 	zip \
# 	pdo \
# 	exif \
# 	pdo_mysql \
# 	zip \
# 	mbstring \
# 	intl \
# 	bcmath \
# 	pcntl

# 	#Redis
# RUN pecl install -o -f redis \
# 	&&  docker-php-ext-enable redis

# 	# Xdebug
# RUN pecl install xdebug \
#     && docker-php-ext-enable xdebug

# 	# Clean up the apt cache and pecl
# RUN rm -rf /tmp/pear \
# 	rm -rf /var/lib/apt/lists/*

# COPY --from=composer /usr/bin/composer /usr/bin/composer

# # Copy PHP configurations
# # COPY --from=base "/${PHP_INI_DIR}/php.ini" "/${PHP_INI_DIR}/php.ini"
# # COPY --from=base "/${PHP_INI_DIR}/conf.d" "/${PHP_INI_DIR}/conf.d"
# COPY ./.docker/php/conf.d "/${PHP_INI_DIR}/conf.d"

# RUN groupadd --gid 1000 appuser \ 
# 	&& useradd --uid 1000 -g appuser \
# 	-G www-data,root --shell /bin/bash \
# 	--create-home appuser

# USER appuser


# Serving image
FROM php:8.1-fpm

# Update dependencies
RUN apt-get update
# Install Zip
RUN apt-get install -y libzip-dev zip && docker-php-ext-install zip
# Install Git
RUN apt-get install -y git
# Install Curl
RUN apt-get install -y libcurl3-dev curl && docker-php-ext-install curl
# Install EXIF
RUN docker-php-ext-install exif
# Install GD
RUN apt-get install -y libfreetype6-dev libjpeg62-turbo-dev libpng-dev \
    && docker-php-ext-configure gd --with-jpeg=/usr/include/ --with-freetype=/usr/include/ && docker-php-ext-install gd
# Install PostgreSQL
RUN apt-get install -y libpq-dev && docker-php-ext-install pdo pdo_pgsql
# Install BC Math
RUN docker-php-ext-install bcmath
# Install internationalization functions
RUN apt-get install -y zlib1g-dev libicu-dev g++ \
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl
# Install Redis extension
RUN pecl install redis \
    && docker-php-ext-enable redis
# Install Process Control extension
RUN docker-php-ext-install pcntl \
    && docker-php-ext-enable pcntl
# Install OPcache extension
RUN docker-php-ext-install opcache
# Install Xdebug
RUN pecl install xdebug \
    && docker-php-ext-enable xdebug
# Clean up the apt and pecl cache
RUN rm -rf /var/lib/apt/lists/* \
	&& rm -rf /tmp/pear

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/local/bin/composer

# Copy PHP configurations
# COPY --from=base "/${PHP_INI_DIR}/php.ini" "/${PHP_INI_DIR}/php.ini"
# COPY --from=base "/${PHP_INI_DIR}/conf.d" "/${PHP_INI_DIR}/conf.d"
COPY ./.docker/php/conf.d "/${PHP_INI_DIR}/conf.d"

RUN groupadd --gid 1000 appuser \ 
	&& useradd --uid 1000 -g appuser \
	-G www-data,root --shell /bin/bash \
	--create-home appuser

USER appuser

# Set up the working directory
WORKDIR /var/www/html