FROM cern/slc6-base:latest

RUN groupadd -r mysql && useradd -r -g mysql mysql

RUN curl -SL "http://ftp.redhat.com/pub/redhat/linux/enterprise/6Server/en/os/SRPMS/mysql-5.1.73-7.el6.src.rpm" -o mysql.src.rpm && \
  mkdir /usr/local/mysql && \
  rpm mysql.src.rpm

ENV PATH $PATH:/usr/local/mysql/bin:/usr/local/mysql/scripts
