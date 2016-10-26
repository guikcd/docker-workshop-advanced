FROM php:5.6-apache
MAINTAINER Theo Chamley <theo.chamley@oxalide.com>

ADD https://symfony.com/installer /usr/local/bin/symfony

RUN apt-get update && apt-get install -y libssl-dev curl git unzip sqlite3 netcat && \
    docker-php-ext-install --jobs $(nproc) pdo_mysql json zip phar && \
    chmod a+x /usr/local/bin/symfony && \
    apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

ADD vhost.conf /etc/apache2/sites-available/000-default.conf
COPY php.ini /usr/local/etc/php/

RUN cd /var/www && \
    symfony demo && \
    chown -R www-data: /var/www/symfony_demo/var && \
    chmod -R 777 /var/www/symfony_demo/var && \
	ls -l /var/www/symfony_demo/var/cache/ && \
	cd /var/www/symfony_demo && php bin/symfony_requirements && \
	php bin/console --env=prod && \
	php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
	php -r "if (hash_file('SHA384', 'composer-setup.php') === 'e115a8dc7871f15d853148a7fbac7da27d6c0030b848d9b3dc09e2a0388afed865e6a3d6b3c0fad45c48e2b5fc1196ae') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" && \
	php composer-setup.php && php -r "unlink('composer-setup.php');" && \
	php composer.phar install && \
	php composer.phar require symfony/assetic-bundle && \
	cat /etc/passwd && \
    rm -r /var/www/symfony_demo/app/config/ /var/www/symfony_demo/var/cache/*

WORKDIR /var/www/symfony_demo

COPY config/ /var/www/symfony_demo/app/config/ 
ADD base.html.twig /var/www/symfony_demo/app/Resources/views/base.html.twig
ADD oxalide.png /var/www/symfony_demo/web/oxalide.png

ENV SYMFONY__DATABASE__USER=root \
    SYMFONY__DATABASE__PASSWORD=toto42 \
    SYMFONY__DATABASE__NAME=symfony_demo \
    SYMFONY__DATABASE__HOST=mysql \
    SYMFONY__DATABASE__PORT=3306
