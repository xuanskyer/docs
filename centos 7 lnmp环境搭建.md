# centos 7 lnmp环境搭建（php一些扩展安装)

> centos 安装好之后，初始化的root用户的密码就是你安装时添加的用户的密码（-_-!!!)
> 避免权限问题，下面所有操作都是以root用户运行。

`su - root`

## 加载自定义配置文件(添加到/etc/bashrc下，若不需要可以不添加)

`vi /etc/bashrc`

例如要加载的文件是：**/etc/myconfig.conf**
在最后添加：

```
if [ -f /etc/myconfig.conf ]; then
	. /etc/myconfig.conf
fi
```

## 更新yum 

`yum update`

## 修改repo源为aliyun的repo源(不需要修改可跳过此步)

`cd /etc/yum.repos.d`

`mv CentOS-Base.repo CentOS-Base.repo-backup`

`wget -O CentOS-Base.repo http://mirrors.aliyun.com/repo/epel-7.repo`

`yum clean all`

`yum makecache`

## 常用软件yum安装

* 安装git : `yum install git`

* 设置常用git alias

```
git config --global color.ui true
  
git config --global alias.co checkout  

git config --global alias.cm commit  

git config --global alias.st status  

git config --global alias.br branch  

git config --global  user.name xxx

git config --global user.email xx@xx.com

```

* 常规包安装

`yum -y install gcc gcc-c++ autoconf libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel libxml2 libxml2-devel zlib zlib-devel glibc glibc-devel glib2 glib2-devel bzip2 bzip2-devel ncurses ncurses-devel curl curl-devel e2fsprogs e2fsprogs-devel krb5 krb5-devel libidn libidn-devel openssl openssl-devel openldap openldap-devel nss_ldap openldap-clients openldap-servers gd gd2 gd-devel gd2-devel perl-CPAN pcre-devel libmcrypt libmcrypt-devel mcrypt mhash`

## 编译安装php、php-fpm、php常用扩展(php版本：5.6.19)

> 为了避免混乱， 最好新建一个目录做下面的操作。如在根目录下创建个download目录。

* 下载源码包

`wget http://cn2.php.net/distributions/php-5.6.19.tar.gz`

`wget https://launchpad.net/libmemcached/1.0/1.0.18/+download/libmemcached-1.0.18.tar.gz`

`wget http://pecl.php.net/get/memcache-2.2.7.tgz`

`wget http://pecl.php.net/get/memcached-2.2.0.tgz`

`wget http://pecl.php.net/get/mongo-1.6.13.tgz`

`wget http://pecl.php.net/get/mongodb-1.1.5.tgz`

`wget http://pecl.php.net/get/redis-2.2.7.tgz`

`wget http://pecl.php.net/get/swoole-1.7.22.tgz`

`wget http://pecl.php.net/get/igbinary-1.2.1.tgz`

`wget https://launchpad.net/libmemcached/1.0/1.0.18/+download/libmemcached-1.0.18.tar.gz`

* 批量解压： `ls *gz | xargs -n1 tar xzvf`

* 编译安装php

解压软件，进入php目录：

> 若为使用aliyun的源，可能需要手动安装一些依赖包

`./configure --prefix=/usr/local/php --with-config-file-path=/etc/php --enable-fpm --enable-pcntl --enable-mysqlnd --enable-opcache --enable-sockets --enable-sysvmsg --enable-sysvsem  --enable-sysvshm --enable-shmop --enable-zip --enable-ftp --enable-soap --enable-xml --enable-mbstring --disable-rpath --disable-debug --disable-fileinfo --with-mysql=mysqlnd --with-mysqli=mysqlnd --with-pdo-mysql=mysqlnd --with-pcre-regex --with-iconv --with-zlib --with-mcrypt --with-gd --with-openssl --with-mhash --with-xmlrpc --with-curl --with-imap-ssl`

`make && make install`

* 添加php的bin、sbin快捷方式

在/etc/bashrc文件最后添加：

```
export PATH=/usr/local/php/bin:$PATH

export PATH=/usr/local/php/sbin:$PATH
```

* 安装libmemcached（php的memcached扩展依赖）

解压libmemcached-1.0.18.tar.gz，进入解压后的目录：

`./configure && make && make install`

* php扩展安装（memcache、memcached、redis、mongo、mongodb、swoole、igbinary）

进入到每个解压后的扩展目录，编译安装：

`phpize && ./configure && make && make install`

* 修改php配置文件

拷贝php源码目录中的php.ini-development 到 /etc/php目录中，重命名为php.ini

在php.ini中添加扩展加载配置：

```
extension=memcache.so

extension=memcached.so

extension=redis.so

extension=mongo.so

extension=mongodb.so

extension=igbinary.so

extension=swoole.so

```

* 添加php-fpm.conf

`cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf`

* 启动php-fpm

`php-fpm -RD`

## 安装nginx

`wget http://nginx.org/download/nginx-1.8.1.tar.gz`

解压后进入nginx目录：

`./configure && make && make install`

添加nginx到PATH，在/etc/bashrc文件最后添加：

`export PATH=/usr/local/nginx/sbin:$PATH`

* 配置nginx

	* nginx.conf 配置示例：

	```
	#user  nobody;
	worker_processes  1;
	
	#error_log  logs/error.log;
	#error_log  logs/error.log  notice;
	#error_log  logs/error.log  info;
	
	#pid        logs/nginx.pid;
	
	events {
	    worker_connections  1024;
	}
	
	
	http {
	    include       mime.types;
	    default_type  application/octet-stream;
	
	    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
	    #                  '$status $body_bytes_sent "$http_referer" '
	    #                  '"$http_user_agent" "$http_x_forwarded_for"';
	
	    access_log  /var/log/nginx/access.log  main;
	
	    sendfile        on;
	    #tcp_nopush     on;
	
	    #keepalive_timeout  0;
	    keepalive_timeout  65;
	
	    #gzip  on;
	
	    # HTTPS server
	    #
	    #server {
	    #    listen       443 ssl;
	    #    server_name  localhost;
	
	    #    ssl_certificate      cert.pem;
	    #    ssl_certificate_key  cert.key;
	
	    #    ssl_session_cache    shared:SSL:1m;
	    #    ssl_session_timeout  5m;
	
	    #    ssl_ciphers  HIGH:!aNULL:!MD5;
	    #    ssl_prefer_server_ciphers  on;
	
	    #    location / {
	    #        root   html;
	    #        index  index.html index.htm;
	    #    }
	    #}
		# 加载虚拟域名配置文件目录下的所有*.conf文件
		include vhost/*.conf; 
	}
	
	```
	
	* 虚拟域名配置示例：
	
	> 该 test.vm.conf 文件在 /usr/local/nginx/conf/vhost/ 目录下
	
	```
	server {
	    listen       80;
	    server_name  www.mytest.vm www.test.vm;
	    access_log  /var/log/nginx/test.vm.access.log;
	    error_log	/var/log/nginx/test.vm.error.log;
	    root /projects/test.vm;
	    index index.php index.html index.htm;
	
	    location / {
	    	try_files $uri $uri/ /index.html;
	    }
	
	    #error_page  404              /404.html;
	
	    error_page   500 502 503 504  /50x.html;
	    location = /50x.html {
	        root   /usr/share/nginx/html;
	    }
	  
	    location ~ \.php$ {
	        fastcgi_pass   127.0.0.1:9000;
	        fastcgi_index  index.php;
	        include	fastcgi.conf;
          include fastcgi_params;
	    }
	
	    location ~ /\.ht {
	        deny  all;
	    }
	}
	
	```
  * 启动nginx
  
  `nginx -c /usr/local/nginx/conf/nginx.conf && nginx -s reload`

> 提示有日志目录不存在，请自行创建

## 安装mysql5.7

* 下载MySQL源码包

`wget http://repo.mysql.com//mysql57-community-release-el7-7.noarch.rpm`

安装rpm：

`rpm -i mysql57-community-release-el7-7.noarch.rpm`

yum安装：

`sudo yum install mysql-community-server`

* 安装完成后，修改root用户密码：

启动： `sudo service mysqld start`

查看状态： `sudo service mysqld status`

获取root当前的密码： `sudo grep 'temporary password' /var/log/mysqld.log`

登录MySQL：

`mysql -uroot -p`

输入密码

修改root密码：

> 新版MySQL的密码格式要求比较变态
> 关于修改默认密码：[修改默认密码](http://dev.mysql.com/doc/mysql-yum-repo-quick-guide/en/)

`ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';`

