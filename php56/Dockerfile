FROM alpine:3.5

LABEL Maintainer="Jan Ritter <git@janrtr.de>" \
      Description="Docker base image optimized for Symfony running apache2 and php5 with composer"

RUN apk --no-cache add php5 \
    php5-common \
	php5-fpm \
	php5-phar \
	php5-pdo \
	php5-json \
	php5-openssl \
	php5-mysql \
	php5-pdo_mysql \
	php5-mcrypt \
	php5-opcache \
	php5-sqlite3 \
	php5-pdo_sqlite \
	php5-ctype \
	php5-zlib \
	php5-curl \
	php5-gd \
	php5-xml \
	php5-dom \
    apache2 \
    supervisor \
    curl

# Apache2 required folder to write the pid
RUN mkdir -p /run/apache2

#Edit apache config
RUN sed -i 's#^DocumentRoot ".*#DocumentRoot "/var/www/html/web"#g' /etc/apache2/httpd.conf && \
    sed -i 's#AllowOverride [Nn]one#AllowOverride All#' /etc/apache2/httpd.conf && \
    #sed -i 's#^ServerRoot .*#ServerRoot "/etc/apache2"#g'  /etc/apache2/httpd.conf && \
    sed -i 's/^#ServerName.*/ServerName webproxy/' /etc/apache2/httpd.conf && \
    #sed -i 's#^IncludeOptional /etc/apache2/conf#IncludeOptional /var/www/config/conf#g' /etc/apache2/httpd.conf && \
    sed -i 's#PidFile "/run/.*#Pidfile "/var/www/run/httpd.pid"#g'  /etc/apache2/conf.d/mpm.conf && \
    sed -i 's#Directory "/var/www/localhost/htdocs.*#Directory "/var/www/html" >#g' /etc/apache2/httpd.conf && \
    sed -i 's#Directory "/var/www/localhost/cgi-bin.*#Directory "/var/www/cgi-bin" >#g' /etc/apache2/httpd.conf && \
    mkdir -p /var/www/html/web && \
    echo "Hello World" >> /var/www/html/web/index.html
#ADD /config/apache.conf /etc/apache2/conf.d/apache.conf


RUN chown -R apache.www-data /var/www

# Configure supervisord
COPY config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Add Composer
RUN curl -sS https://getcomposer.org/installer | php && mv composer.phar /usr/local/bin/composer

EXPOSE 80 443
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]

