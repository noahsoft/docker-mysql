FROM ubuntu:trusty

MAINTAINER Bastien Semene <bastien@semene.fr>

# add our user and group
RUN groupadd -r mysql && useradd -r -g mysql mysql

# Add MySQL configuration
ADD my.cnf /etc/mysql/conf.d/my.cnf
ADD mysqld_charset.cnf /etc/mysql/conf.d/mysqld_charset.cnf

#Create dir for config and datas
RUN mkdir -p /var/lib/mysql
RUN chown -R mysql:mysql /etc/mysql /var/lib/mysql

# Add VOLUMEs to allow backup of config and databases
VOLUME ["/etc/mysql", "/var/lib/mysql"]