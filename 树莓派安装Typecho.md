# 树莓派安装Typecho 个人博客
之前在自己的vps上安装成功了centos系统，然后在树莓派的Debian也测试安装成功

## 1更新源地址为阿里云源。
编辑源：
sudo nano /etc/apt/sources.list
打开以后，替换原来源中的链接为阿里云源，替换后大概会如下：

deb http://mirrors.aliyun.com/raspbian/raspbian/ wheezy main non-free contrib
deb-src http://mirrors.aliyun.com/raspbian/raspbian/ wheezy main non-free contrib
替换后，更新源：
sudo apt-get update
更新后，更新已安装包：
sudo apt-get upgrade
更新完以后，树莓派基本上配置完成了！

 
## 安装和配置 Nginx/PHP/SQLite3
考虑到树莓派性能比较弱鸡，如果使用MySql或者其他数据库，那基本上每天都会宕机，综合考虑还是适合Nginx+SQLite3的配置较为合适，当然PHP是必不可少的。
安装nginx：
sudo apt-get install nginx
安装SQLite3：
sudo apt-get install sqlite3
安装PHP（PHP5）：
sudo apt-get install php5-fpm
安装PHP5的SQLite支持：
sudo apt-get install php5-sqlite
安装PHP-APC优化：
sudo apt-get install php-apc

## 安装完毕，开始配置。

配置 Nginx：
nano /etc/nginx/nginx.conf
打开以后找到下面的位置，并修改：
```
user www-data; 
worker_processes 1; #修改这里 
pid /var/run/nginx.pid; 
events { 
   worker_connections 64; #修改这里 
   #multi_accept on; 
}
继续向后找到 gzip 去掉前面的注释 修改如下:

- gzip on; 
gzip_disable “msie6”; 
gzip_vary on; 
gzip_proxied any; 
gzip_comp_level 6; 
gzip_buffers 16 8k; 
gzip_http_version 1.1; 
gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;
```
保存并退出 nginx.conf。

配置 PHP：
nano /etc/php5/fpm/php.ini
打开以后找到下面的位置，并修改：
```
; Maximum amount of memory a script may consume (128MB) 
;http://php.net/memory-limit 
memory_limit = 32M #修改这里
继续向后面找或者手动搜索关键字，找到下面的位置，并修改：

; cgi.fix_pathinfo provides real PATH_INFO/PATH_TRANSLATED support for CGI. PHP’s 
; previous behaviour was to set PATH_TRANSLATED to SCRIPT_FILENAME, and to not grok 
; what PATH_INFO is. For more information on PATH_INFO, see the cgi specs. Setting 
; this to 1 will cause PHP CGI to fix its paths to conform to the spec. A setting 
; of zero causes PHP to behave as before. Default is 1. You should fix your scripts 
; to use SCRIPT_FILENAME rather than PATH_TRANSLATED. 
;http://php.net/cgi.fix-pathinfo 
cgi.fix_pathinfo=1 #修改这里
```
保存并退出 php.ini。

配置PHP-FPM：
nano /etc/php5/fpm/php-fpm.conf

找到下面的位置，并修改：
```
; The maximum number of processes FPM will fork. This has been design to control 
; the global number of processes when using dynamic PM within a lot of pools. 
; Use it with caution. 
; Note: A value of 0 indicates no limit 
; Default Value: 0 
process.max = 4 #修改这里
```
保存并退出 php-fpm.conf。

配置网站的相关配置，先备份一份较为安全：
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default.bak
修改配置文件：
sudo nano /etc/nginx/sites-available/default
一般来说，80端口是默认启用的，如果前面有#，那就去掉启用：

listen 80;
下面这一行需要增加一句index.php

index index.html index.htm;
变成：

index index.php index.html index.htm;
接着往下翻页继续修改
```
#location ~ \.php$ {
#       fastcgi_split_path_info ^(.+\.php)(/.+)$;
#       # NOTE: You should have "cgi.fix_pathinfo = 0;" in php.ini
#
#       # With php5-cgi alone:
#       fastcgi_pass 127.0.0.1:9000;
#       # With php5-fpm:
#       fastcgi_pass unix:/var/run/php5-fpm.sock;
#       fastcgi_index index.php;
#       include fastcgi_params;
#}
改为：

if (!-e $request_filename) {
    rewrite ^(.*)$ /index.php$1 last;
}
location ~ \.php$ {
#       fastcgi_split_path_info ^(.+\.php)(/.+)$;
#       # NOTE: You should have "cgi.fix_pathinfo = 0;" in php.ini
#
#       # With php5-cgi alone:
#       fastcgi_pass 127.0.0.1:9000;
#       # With php5-fpm:
       fastcgi_pass unix:/var/run/php5-fpm.sock;
       fastcgi_index index.php;
       include fastcgi_params;
}
```
修改完成以后，测试下PHP是否已经能正常支持
sudo nano /etc/home/pi/www/index.php #路径为之前配置里面定义的root根目录
输入如下内容：
```
<?php
      phpinfo();
?>
```
重启nginx，和php5-fpm
sudo /etc/init.d/nginx restart
sudo /etc/init.d/php5-fpm restart

局域网的电脑中输入树莓派的IP地址查看是否成功：

http://192.169.100.5/index.php

## 安装Typecho
上面的配置结束以后，基本上是一个已经能正常运行的环境了，但为了能顺利安装，还需要给文件夹目录授权：
sudo chmod 755 /home/pi/www -R

授权后下载最新的安装包并解压，解压完移动目录：
```
cd /home/pi/www
wget http://typecho.org/downloads/1.1-17.10.30-release.tar.gz
tar zxvf 1.1-17.10.30-release.tar.gz
mv build/* /home/pi/www
```
然后就按照官方给出的教程自己设置就 OK 了。

