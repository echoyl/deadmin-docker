# 基于官方 PHP 镜像（示例）
FROM php:8.2-fpm


ENV COMPOSER_ALLOW_SUPERUSER=1

# 系统依赖与 php 扩展编译依赖（增加 imagick / curl / xlswriter 所需包）
RUN set -eux; \
    if [ -f /etc/apt/sources.list ]; then \
    sed -i 's@http://deb.debian.org@https://mirrors.ustc.edu.cn@g; s@http://security.debian.org@https://mirrors.ustc.edu.cn@g; s@http://archive.ubuntu.com@https://mirrors.ustc.edu.cn@g; s@http://security.ubuntu.com@https://mirrors.ustc.edu.cn@g' /etc/apt/sources.list; \
    fi; \
    find /etc/apt/sources.list.d -type f \( -name "*.list" -o -name "*.sources" \) -print0 | xargs -0 -r sed -i 's@http://deb.debian.org@https://mirrors.ustc.edu.cn@g; s@http://security.debian.org@https://mirrors.ustc.edu.cn@g; s@http://archive.ubuntu.com@https://mirrors.ustc.edu.cn@g; s@http://security.ubuntu.com@https://mirrors.ustc.edu.cn@g'; \
    apt-get update --fix-missing && apt-get install -y \
    git unzip curl ca-certificates \
    libzip-dev libonig-dev libpng-dev libjpeg-dev libfreetype6-dev \
    libicu-dev zlib1g-dev libxml2-dev default-libmysqlclient-dev default-mysql-client \
    libcurl4-openssl-dev imagemagick libmagickwand-dev pkg-config libxlsxwriter-dev \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-configure intl \
    && docker-php-ext-install -j$(nproc) \
    pdo pdo_mysql mysqli mbstring exif pcntl bcmath intl gd zip opcache xml sockets curl fileinfo \
    && pecl install redis imagick xlswriter \
    && docker-php-ext-enable redis imagick xlswriter \
    && rm -rf /var/lib/apt/lists/*

# 安装 composer
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

WORKDIR /var/www/html

# 注意：composer create-project 已从这里移除，改为单独服务执行
RUN chown -R www-data:www-data /var/www/html

RUN printf '#!/bin/sh\nexec /usr/bin/mysql --ssl=OFF "$@"\n' > /usr/local/bin/mysql \
    && chmod +x /usr/local/bin/mysql

EXPOSE 9000
CMD ["php-fpm"]