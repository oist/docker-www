FROM cern/slc6-base:latest

# Create user for apache, we kind of hijack this user by creating it before the
# httpd package does, so we can keep control of the user id owning the files in
# the volumes, which are a PITA to manage.
RUN groupadd -g 82 apache && \
    useradd -u 82 -g apache apache

# Install all the specific versions and lock them in.
RUN rpm --rebuilddb && \
	  yum --setopt=tsflags=nodocs -y install \
	    httpd-2.2.15-54.el6_8 \
      httpd-tools-2.2.15-54.el6_8 \
	    mod_ssl-2.2.15-54.el6_8 \
	    php-5.3.3-47.el6 \
	    php-cli-5.3.3-47.el6 \
			php-common-5.3.3-47.el6 \
			php-devel-5.3.3-47.el6 \
			php-gd-5.3.3-47.el6 \
			php-ldap-5.3.3-47.el6 \
			php-mbstring-5.3.3-47.el6 \
			php-mysql-5.3.3-47.el6 \
			php-pear-1.9.4-5.el6 \
	    php-pecl-apc-3.1.9-2.el6 \
			php-pecl-memcached-1.0.0-1.el6 \
			php-pdo-5.3.3-47.el6 \
			php-xml-5.3.3-47.el6 \
      # We can't install Xdebug in the usual way (pecl) because it doesn't
      # support PHP 5.3 anymore.
      php-pecl-xdebug \
      mysql-5.1.73-7.el6 \
      gcc \
      gcc-c++ \
			git \
      tar \
      which && \
	  yum versionlock add \
	    httpd \
	    mod_ssl \
	    php* && \
	  rm -rf /var/cache/yum/* && \
    yum clean all

# Install Composer - version specific.
ENV COMPOSER_VERSION 1.2.0
RUN	curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer --version=${COMPOSER_VERSION} && \
  chmod +x /usr/local/bin/composer

# Install Drush using Composer. 7.3.0 is the newest version that supports PHP
# 5.3
ENV DRUSH_VERSION 7.3.0
RUN git clone https://github.com/drush-ops/drush.git /usr/local/src/drush && \
    cd /usr/local/src/drush && \
    git checkout ${DRUSH_VERSION} && \
    ln -s /usr/local/src/drush/drush /usr/bin/drush && \
    composer install
RUN drush --version
# Add drush development tools
RUN drush @none dl registry_rebuild

# Create workdir and manage permisssions.
RUN mkdir -p /var/www/html && \
    chown -R apache:apache /var/www && \
    chmod -R 774 /var/www && \
    umask 774 /var/www
WORKDIR /var/www/html

# COPY all the configuration to the image.
COPY conf/httpd/conf.d/000-wwprod.conf /etc/httpd/conf.d/
COPY conf/httpd/conf.d/php.conf /etc/httpd/conf.d/
COPY conf/httpd/conf.d/ssl.conf /etc/httpd/conf.d/
COPY conf/httpd/httpd.conf /etc/htppd/conf/
COPY conf/httpd/ssl/www.crt /etc/ssl/certs/
COPY conf/httpd/ssl/www.key /etc/ssl/certs/
COPY conf/php/php.ini /etc/
COPY conf/php/conf.d/xdebug.ini /etc/php.d/
COPY conf/php/conf.d/apc.ini /etc/php.d/

EXPOSE 80 443

COPY run.sh /tmp/run.sh
RUN chmod +x /tmp/run.sh
ENTRYPOINT ["/tmp/run.sh"]
