# A tomcat+mysql image
[![](https://images.microbadger.com/badges/image/zer0i3/tomcat8-mysql.svg)](https://microbadger.com/images/zer0i3/tomcat8-mysql)[![](https://images.microbadger.com/badges/version/zer0i3/tomcat8-mysql.svg)](https://microbadger.com/images/zer0i3/tomcat8-mysql)[![Build Status](https://travis-ci.org/zer0i3/tomcat8-mysql-docker.svg?branch=master)](https://travis-ci.org/zer0i3/tomcat8-mysql-docker)

A custom image for deploying Java web project based on [baseimage]( https://github.com/phusion/baseimage-docker), plus:

* Set locales en_US.UTF-8
* Change apt source to tsinghua
* JDK8
* Tomcat8.5.37
* Default mysql in ubuntu16.04

## Usage

Start a container:

```shell
docker run -itd --rm -p 8080:8080 zer0i3/tomcat8-mysql
```

Config mysql from environment:

```shell
docker run -itd --rm -p 8080:8080 --env MYSQL_USER=user --env MYSQL_USER_PWD=password zer0i3/tomcat8-mysql
```

Environment variables available: 

```shell
MYSQL_ROOT_PWD # set to change root password
MYSQL_USER	
MYSQL_USER_PWD # create user with password
MYSQL_USER_DB # create db and grant privileges 
```

Deploy Java web project:

> Just move your project to `/opt/tomcat/webapps/`
>
> A example here, [imxss](https://github.com/zer0i3/imxss-docker) based `zer0i3/tomcat8-mysql`