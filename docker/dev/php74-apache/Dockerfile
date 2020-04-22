FROM ubuntu:18.04
# noninteractive: It never interacts with you  at  all, and  makes  the  default  answers  be used for all questions.
ENV DEBIAN_FRONTEND noninteractive
# Install basics
RUN apt-get update --fix-missing
RUN apt-get install -y software-properties-common && \
add-apt-repository ppa:ondrej/php && apt-get update
RUN apt-get install -y curl
RUN apt-get install -y git
RUN apt-get install -y php-pear
RUN apt-get install libcurl3-openssl-dev
# Install PHP 7.4
RUN apt-get install -y php7.4 php7.4-mysql php7.4-cli php7.4-gd php7.4-curl php7.4-xml php7.4-zip php7.4-mbstring php7.4-dev
# Apache: allow .htaccess with RewriteEngine
RUN a2enmod rewrite
# activate php7.4
RUN a2enmod php7.4

# Install PDFlib
RUN curl -SL "https://www.pdflib.com/binaries/PDFlib/920/PDFlib-9.2.0p5-Linux-x86_64-php.tar.gz" -o pdflib-php.tar.gz
RUN tar xzf pdflib-php.tar.gz && \
  cd PDFlib-9.2.0p5-Linux-x86_64-php/ && \
  cp bind/php/php-740-nts/php_pdflib.so /usr/lib/php/20190902/

RUN echo "extension=php_pdflib.so" >> /etc/php/7.4/apache2/php.ini
RUN echo "extension=php_pdflib.so" >> /etc/php/7.4/cli/php.ini

#Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN composer --version

# Update the PHP.ini file, enable <? ?> tags and quieten logging.
RUN sed -i "s/short_open_tag = .*/short_open_tag = On/" /etc/php/7.4/apache2/php.ini
RUN sed -i "s/short_open_tag = .*/short_open_tag = On/" /etc/php/7.4/cli/php.ini
RUN sed -i "s/error_reporting = .*$/error_reporting = E_ERROR | E_WARNING | E_PARSE/" /etc/php/7.4/cli/php.ini
# Manually set up the apache environment variables
ENV APACHE_LOG_DIR /var/log/apache2
ENV APACHE_LOCK_DIR /var/lock/apache2
ENV APACHE_PID_FILE /var/run/apache2.pid

# Update some properties of php.ini
RUN echo "error_log = /var/log/php_errors.log" >> /etc/php/7.4/apache2/php.ini
RUN sed -i "s/post_max_size = .*/post_max_size = 100M/" /etc/php/7.4/apache2/php.ini


# Xdebug
RUN apt-get install php-xdebug
RUN echo 'xdebug.remote_host=localhost' >> /etc/php/7.4/apache2/conf.d/20-xdebug.ini
RUN echo 'xdebug.remote_port=9000' >> /etc/php/7.4/apache2/conf.d/20-xdebug.ini
RUN echo 'xdebug.remote_enable=1' >> /etc/php/7.4/apache2/conf.d/20-xdebug.ini
RUN echo 'xdebug.remote_connect_back=1' >> /etc/php/7.4/apache2/conf.d/20-xdebug.ini


# Expose apache.
EXPOSE 80
EXPOSE 8080
EXPOSE 443
EXPOSE 3306
# Update the default apache site with the config we created.
ADD apache-config.conf /etc/apache2/sites-enabled/000-default.conf


RUN mkdir -p /var/www/logs
RUN chmod -R 777 /var/www/logs
RUN chmod -R 777 /var/www/html

RUN chown -R www-data:www-data /var/www
RUN mkdir /temp
RUN chmod 777 /temp

# Change uid of user "www-data" by 1000
# RUN usermod -u 1000 www-data

# Add user
RUN addgroup --gid 1000 appuser
RUN adduser --disabled-password --gecos '' --uid 1000 --gid 1000 appuser


WORKDIR /var/www/html

# By default start up apache in the foreground, override with /bin/bash for interative.
CMD /usr/sbin/apache2ctl -D FOREGROUND
