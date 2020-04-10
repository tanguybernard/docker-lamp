FROM ubuntu:18.04

ENV DEBIAN_FRONTEND noninteractive
# Install basics
RUN apt-get update
RUN apt-get install -y software-properties-common && \
add-apt-repository ppa:ondrej/php && apt-get update
RUN apt-get install -y curl
# Install PHP 5.6
RUN apt-get install -y php5.6 php5.6-mysql php5.6-mcrypt php5.6-cli php5.6-gd php5.6-curl
# Apache: allow .htaccess with RewriteEngine
RUN a2enmod rewrite
# activate php5.6
RUN a2enmod php5.6

# Update the PHP.ini file, enable <? ?> tags and quieten logging.
# RUN sed -i “s/short_open_tag = Off/short_open_tag = On/” /etc/php/5.6/apache2/php.ini
RUN sed -i "s/short_open_tag = .*/short_open_tag = On/" /etc/php/5.6/apache2/php.ini
RUN sed -i "s/error_reporting = .*$/error_reporting = E_ERROR | E_WARNING | E_PARSE/" /etc/php/5.6/apache2/php.ini
# Manually set up the apache environment variables
ENV APACHE_LOG_DIR /var/log/apache2
ENV APACHE_LOCK_DIR /var/lock/apache2
ENV APACHE_PID_FILE /var/run/apache2.pid

# Update some properties of php.ini
RUN echo "error_log = /var/log/php_errors.log" >> /etc/php/5.6/apache2/php.ini
RUN sed -i "s/post_max_size = .*/post_max_size = 100M/" /etc/php/5.6/apache2/php.ini


# Xdebug
RUN apt-get install php-xdebug
RUN echo 'xdebug.remote_host=localhost' >> /etc/php/5.6/apache2/conf.d/20-xdebug.ini
RUN echo 'xdebug.remote_port=9000' >> /etc/php/5.6/apache2/conf.d/20-xdebug.ini
RUN echo 'xdebug.remote_enable=1' >> /etc/php/5.6/apache2/conf.d/20-xdebug.ini
RUN echo 'xdebug.remote_connect_back=1' >> /etc/php/5.6/apache2/conf.d/20-xdebug.ini


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
RUN usermod -u 1000 www-data
# RUN usermod -G staff www-data


# By default start up apache in the foreground, override with /bin/bash for interative.
CMD /usr/sbin/apache2ctl -D FOREGROUND

