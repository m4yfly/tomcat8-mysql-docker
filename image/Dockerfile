FROM phusion/baseimage:0.10.2

MAINTAINER zer0i3 <zer0i3@aliyun.com>

ENV TOMCAT_VERSION 8.5.37

#Set locales
RUN sed -i "s/http:\/\/archive.ubuntu.com/http:\/\/mirrors.tuna.tsinghua.edu.cn/g" /etc/apt/sources.list && \
    apt-get update && \
    apt-get install -y locales && \
    locale-gen en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LC_ALL en_US.UTF-8

#Fix sh
RUN rm /bin/sh && ln -s /bin/bash /bin/sh

#Install dependencies
RUN apt-get -y dist-upgrade && \
    apt-get -y install software-properties-common && \
    apt-get -y install curl wget

#Install JDK8
RUN echo oracle-java8-installer shared/accepted-oracle-license-v1-1 select true | debconf-set-selections && \
    add-apt-repository ppa:webupd8team/java && \
    apt-get update && \
    apt-get install -y oracle-java8-installer && \
    apt-get install oracle-java8-set-default && \
    rm -rf /var/lib/apt/lists/* && \
    rm -rf /var/cache/oracle-jdk8-installer

#Define commonly used JAVA_HOME variable
ENV JAVA_HOME /usr/lib/jvm/java-8-oracle

#Install Tomcat8
RUN wget --quiet --no-cookies http://mirrors.shu.edu.cn/apache/tomcat/tomcat-8/v${TOMCAT_VERSION}/bin/apache-tomcat-${TOMCAT_VERSION}.tar.gz -O /tmp/tomcat.tgz && \
    tar xzvf /tmp/tomcat.tgz -C /opt && \
    mv /opt/apache-tomcat-${TOMCAT_VERSION} /opt/tomcat && \
    rm /tmp/tomcat.tgz && \
    rm -rf /opt/tomcat/webapps/examples && \
    rm -rf /opt/tomcat/webapps/docs && \
    rm -rf /opt/tomcat/webapps/host-manager && \
    rm -rf /opt/tomcat/webapps/manager

ENV CATALINA_HOME /opt/tomcat
ENV PATH $PATH:$CATALINA_HOME/bin
EXPOSE 8080

#Config tomcat
COPY ./catalina.sh /etc/service/catalina/run
RUN chmod +x /etc/service/catalina/run && \
    chgrp -R www-data /opt/tomcat && \
    chmod -R g+r /opt/tomcat/conf && \
    chmod g+x /opt/tomcat/conf && \
    chown -R www-data /opt/tomcat/webapps/ /opt/tomcat/work/ /opt/tomcat/temp/ /opt/tomcat/logs/

#Install Mysql
RUN echo "mysql-server mysql-server/root_password password root" | debconf-set-selections && \
    echo "mysql-server mysql-server/root_password_again password root" | debconf-set-selections && \
    apt-get update && \
    apt-get install -y mysql-server && \
    mkdir -p /var/lib/mysql && \
	mkdir -p /var/run/mysqld && \
	mkdir -p /var/log/mysql && \
	chown -R mysql:mysql /var/lib/mysql && \
	chown -R mysql:mysql /var/run/mysqld && \
	chown -R mysql:mysql /var/log/mysql && \
    usermod -d /var/lib/mysql/ mysql && \
    apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

#UTF-8 and bind-address
RUN sed -i -e "$ a [client]\n\n[mysql]\n\n[mysqld]"  /etc/mysql/my.cnf && \
	sed -i -e "s/\(\[client\]\)/\1\ndefault-character-set = utf8/g" /etc/mysql/my.cnf && \
	sed -i -e "s/\(\[mysql\]\)/\1\ndefault-character-set = utf8/g" /etc/mysql/my.cnf && \
	sed -i -e "s/\(\[mysqld\]\)/\1\ninit_connect='SET NAMES utf8'\ncharacter-set-server = utf8\ncollation-server=utf8_unicode_ci\nbind-address = 0.0.0.0/g" /etc/mysql/my.cnf
VOLUME /var/lib/mysql
EXPOSE 3306

WORKDIR /opt/tomcat/webapps/

#Config mysql and start service
COPY ./20_ubuntu_tomcat8_mysql.sh /etc/my_init.d/20_ubuntu_tomcat8_mysql.sh
RUN chmod +x /etc/my_init.d/20_ubuntu_tomcat8_mysql.sh
