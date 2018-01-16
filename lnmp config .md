# php mysql 修改配置

## 查看Nginx安装目录和配置文件

1查看nginx目录默认是在 /usr/local/nginx/sbin/nginx 
``` ps aux | grep nginx ```

2 使用 ``` /usr/local/nginx/sbin/nginx -V ``` 查看配置文件地址

进入 ``` /usr/local/nginx/  ```

打开nginx.conf 文件 ``` cat nginx.conf ```查看server 配置项 
找到网站根目录 

## 修复phpmyadmin无法登陆的问题

1 登陆的时候会提示错误信息 百度查看之后发现需要给一个目录进行授权

" 出现这个问题的原因是没有生成 mysql.sock 文件，没有生成这个文件的原因是 /usr/local/mysql 目录的权限不对： "

 ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/tmp/mysql.sock' (2)
  
2 使用以下命令给目录授权

``` 
chown -R mysql:mysql  /usr/local/mysql 
```

3 重启mysql /etc/init.d/mysql restart 然后就可以正常使用了

## 
 