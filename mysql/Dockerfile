FROM cern/slc6-base:latest

# Install all the specific versions and lock them in.
RUN rpm --rebuilddb && \
	  yum --setopt=tsflags=nodocs -y install \
      mysql-5.1.73-7.el6 \
      mysql-server-5.1.73-7.el6 && \
    yum versionlock add \
	    mysql \
	    mysql-server && \
	  rm -rf /var/cache/yum/* && \
    yum clean all

ENV PATH $PATH:/usr/bin/mysql:/usr/libexec/

VOLUME /var/lib/mysql

# COPY configuration
COPY conf/mysql.cnf /etc/mysql/my.cnf

COPY docker-entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]

EXPOSE 3306
CMD ["mysqld", "--datadir=/var/lib/mysql", "--user=mysql"]
