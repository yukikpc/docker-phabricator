FROM php:7.4-apache
MAINTAINER Yûki K<yukikpc@gmail.com>

ARG phabricator_commit_hash="ff6f24db2bc016533bca9040954a218c54ca324e"

RUN apt-get update && apt-get -y upgrade && \
	apt-get -y install git python3-pygments zlib1g-dev libpng-dev libjpeg-dev libfreetype6-dev libzip-dev libldap2-dev netcat && \
	docker-php-ext-configure gd --with-jpeg --with-freetype && \
	docker-php-ext-install mysqli pcntl gd zip opcache ldap && \
	printf "\n" | pecl install apcu && \
	pecl install xhprof && \
	a2enmod rewrite && \
	cd /usr/local/etc/php/conf.d && \
	echo "extension=apcu.so" > pecl-apcu.ini && \
	echo "extension=xhprof.so" > pecl-xhprof.ini && \
	cd - && \
	curl -sL https://deb.nodesource.com/setup_13.x | bash - && \
	apt-get install -y nodejs && \
	cd /opt && \
	git clone --single-branch --branch stable https://github.com/phacility/phabricator && \
	git clone --single-branch --branch stable https://github.com/phacility/arcanist && \
	git clone --single-branch --branch stable https://github.com/phacility/libphutil && \
	git clone --single-branch https://github.com/ProfFan/phabritex && \
	cd - && \
	cd /var/www && \
	git clone https://github.com/ProfFan/render2katex && \
	cd - && \
	cd /var/www/render2katex && \
	npm install --product && \
	cd - && \
	cd /opt/phabricator && \
	git checkout $phabricator_commit_hash && \
	cp -r /opt/phabritex/src/extensions/* src/extensions && \
	cp -r /opt/phabritex/webroot/rsrc/* webroot/rsrc && \
	sed -i '3i \ * @requires katex-css' webroot/rsrc/css/core/remarkup.css && \
	sed -i '3i \ * @requires katex-css' webroot/rsrc/css/phui/phui-document.css && \
	cd - && \
	apt-get clean && \
	rm -rf /var/lib/apt/lists/

COPY ./000-default.conf /etc/apache2/sites-available/
COPY ./php-user.ini /usr/local/etc/php/conf.d/
COPY ./docker-entrypoint.sh /usr/local/bin/

EXPOSE 80
WORKDIR /opt/phabricator

ENTRYPOINT ["docker-entrypoint.sh"]
# CMD ["/usr/bin/supervisord", "-c", "/etc/supervisord.conf"]
CMD ["apache2-foreground"]