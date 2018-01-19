FROM alpine:3.3

LABEL Maintainer="Jan Ritter <git@janrtr.de>" \
      Description="Docker base image optimized for Symfony running apache2, php5.6 with composer, phpunit and xdebug"

RUN apk --no-cache add apache2 \
    apache2-ssl \
    php-apache2 \
    openssl \
    php \
    php-common \
	php-fpm \
	php-phar \
	php-pdo \
	php-json \
	php-openssl \
	php-mysql \
	php-pdo_mysql \
	php-mcrypt \
	php-opcache \
	php-sqlite3 \
	php-pdo_sqlite \
	php-ctype \
	php-zlib \
	php-curl \
	php-gd \
	php-xml \
	php-dom \
    php-openssl \
	php-soap \
	php-dev \
    php-phar \
    php-cli \
    supervisor \
    autoconf \
    gcc \
    g++ \
    zlib-dev \
    file \
    libc-dev  \
    make pkgconf \
    tar \
    php-pear \
    tzdata \
    libmemcached-dev \
    git \
    libmemcached \
    curl

# Apache2 required folder to write the pid
RUN mkdir -p /run/apache2

#Edit apache config
RUN sed -i 's#^DocumentRoot ".*#DocumentRoot "/var/www/html/web"#g' /etc/apache2/httpd.conf && \
    sed -i 's#AllowOverride [Nn]one#AllowOverride All#' /etc/apache2/httpd.conf && \
    sed -i 's/^#ServerName.*/ServerName webproxy/' /etc/apache2/httpd.conf && \
	sed -i 's/#LoadModule rewrite_module/LoadModule rewrite_module/g' /etc/apache2/httpd.conf && \
    sed -i 's#PidFile "/run/.*#Pidfile "/var/www/run/httpd.pid"#g'  /etc/apache2/conf.d/mpm.conf && \
    sed -i 's#Directory "/var/www/localhost/htdocs.*#Directory "/var/www/html" >#g' /etc/apache2/httpd.conf && \
    sed -i 's#Directory "/var/www/localhost/cgi-bin.*#Directory "/var/www/cgi-bin" >#g' /etc/apache2/httpd.conf && \
	sed -i 's#ErrorLog logs/error.log #ErrorLog /dev/stderr#g' /etc/apache2/httpd.conf && \
    mkdir -p /var/www/html/web && \
    echo "<?php echo 'Hello World from PHP'; ?>" >> /var/www/html/web/index.php

#Edit ssl config
RUN sed -i 's#^DocumentRoot ".*#DocumentRoot "/var/www/html/web"#g' /etc/apache2/conf.d/ssl.conf

#RUN chown -R apache.apache /var/www

# Configure supervisord
COPY config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Add Composer
RUN curl -sS https://getcomposer.org/installer | php && mv composer.phar /usr/local/bin/composer

# Add phpunit
RUN wget https://phar.phpunit.de/phpunit.phar
RUN chmod +x phpunit.phar
RUN mv phpunit.phar /usr/local/bin/phpunit

# Add xdebug
RUN cd /tmp && wget http://xdebug.org/files/xdebug-2.5.5.tgz \
    && tar -zxvf xdebug-2.5.5.tgz \
    && cd xdebug-2.5.5 && phpize \
    && ./configure --enable-xdebug && make && make install \
    && echo "zend_extension=$(find /usr/lib/php5/modules/ -name xdebug.so)" >> /etc/php/php.ini \
    && echo "xdebug.remote_enable=on" >> /etc/php/php.ini \
    && echo "xdebug.remote_handler=dbgp" >> /etc/php/php.ini \
    && echo "xdebug.remote_connect_back=1" >> /etc/php/php.ini \
    && echo "xdebug.remote_autostart=on" >> /etc/php/php.ini \
    && echo "xdebug.idekey=\"PHPSTORM\"" >> /etc/php/php.ini \
    && echo "xdebug.remote_port=9000" >> /etc/php/php.ini

EXPOSE 80 443 9000
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]

