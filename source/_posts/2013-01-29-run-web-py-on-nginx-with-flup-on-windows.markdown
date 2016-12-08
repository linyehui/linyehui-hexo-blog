---
title: 'Windows下通过Nginx+Flup来运行Web.py'
layout: post
tags:
---

在Windows下搞这些东西纯属娱乐，虽然上不了台面，但简单够用，这就足够了。

### 前言

0、本文的前提是你已经知道什么是[Web.py](http://webpy.org/)并且知道怎么运行Web.py的代码。  

1、本文是对Web.py Cookbook中[Webpy + Nginx with FastCGI](http://webpy.org/cookbook/fastcgi-nginx)这一章节的实践操作指南  

2、所以本文假设你已经在本机安装好了python和web.py并能正常运行  


### 配置指南

**1、下载并配置Nginx**  

1.1 Nginx专门有Windows的zip包，直接从[官网](http://nginx.org/en/download.html)下载即可：[nginx/Windows-1.2.6](http://nginx.org/download/nginx-1.2.6.zip)  


1.2 下载后解压到你喜欢的任何目录  

1.3 打开nginx.conf进行配置，我的配置如下：  


```shell
    worker_processes  1;
     
    #error_log  logs/error.log;
    #error_log  logs/error.log  notice;
    #error_log  logs/error.log  info;
     
    #pid        logs/nginx.pid;
     
     
    events {
        worker_connections  64;
    }
     
     
    http {
        include       mime.types;
        default_type  application/octet-stream;
     
        #log_format  main  '$remote_addr - $remote_user [$time_local] $request '
        #                  '"$status" $body_bytes_sent "$http_referer" '
        #                  '"$http_user_agent" "$http_x_forwarded_for"';
     
        #access_log  logs/access.log  main;
     
        sendfile        on;
        #tcp_nopush     on;
     
        #keepalive_timeout  0;
        keepalive_timeout  10;
     
        #gzip  on;
     
        server {
            listen       80;
            server_name  localhost;
           
            #root   /cygdrive/D/html;
            root   D:/git/learning/HelloWebPy/HelloWorld;
            index  index.html index.htm;
     
            charset utf-8;
     
            #access_log  logs/host.access.log  main;
            
            location = /favicon.ico {
    	    rewrite ^/favicon.ico$  /static/favicon.ico break;
            }
    
            # 静态资源
            location ~* ^.+/.(html|jpg|jpeg|gif|png|ico|css|zip|tgz|gz|rar|bz2|doc|xls|exe|pdf|ppt|txt|tar|mid|midi|wav|bmp|rtf|js)$
            {
                    expires 30d;
                    break;
            }
     
            location / {
                # 指定 fastcgi 的主机和端口
                fastcgi_pass 127.0.0.1:8051;
                fastcgi_param PATH_INFO $fastcgi_script_name;
                fastcgi_param REQUEST_METHOD $request_method;
                fastcgi_param QUERY_STRING $query_string;
                fastcgi_param CONTENT_TYPE $content_type;
                fastcgi_param CONTENT_LENGTH $content_length;
                fastcgi_param SERVER_PROTOCOL $server_protocol;
                fastcgi_param SERVER_PORT $server_port;
                fastcgi_param SERVER_NAME $server_name;
                fastcgi_pass_header Authorization;
                fastcgi_intercept_errors off;
            }
     
            #error_page  404              /404.html;
     
            # redirect server error pages to the static page /50x.html
            #
            error_page   500 502 503 504  /50x.html;
            location = /50x.html {
                root   html;
            }
     
            # proxy the PHP scripts to Apache listening on 127.0.0.1:80
            #
            #location ~ /.php$ {
            #    proxy_pass   http://127.0.0.1;
            #}
     
            # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
            #
            #location ~ /.php$ {
            #    root           html;
            #    fastcgi_pass   127.0.0.1:9000;
            #    fastcgi_index  index.php;
            #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
            #    include        fastcgi_params;
            #}
     
            # deny access to .htaccess files, if Apache's document root
            # concurs with nginx's one
            #
            #location ~ //.ht {
            #    deny  all;
            #}
        }
       # another virtual host using mix of IP-, name-, and port-based configuration
       #
       #server {
       #    listen       8000;
       #    listen       somename:8080;
       #    server_name  somename  alias  another.alias;
       #    location / {
       #        root   html;
       #        index  index.html index.htm;
       #    }
       #}
       # HTTPS server
       #
       #server {
       #    listen       443;
       #    server_name  localhost;
       #    ssl                  on;
       #    ssl_certificate      cert.pem;
       #    ssl_certificate_key  cert.key;
       #    ssl_session_timeout  5m;
       #    ssl_protocols  SSLv2 SSLv3 TLSv1;
       #    ssl_ciphers  ALL:!ADH:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP;
       #    ssl_prefer_server_ciphers   on;
       #    location / {
       #        root   html;
       #        index  index.html index.htm;
       #    }
       #}
    }
```

1.4 运行nginx.exe，并在浏览器输入127.0.0.1验证下nginx是否正常启动  


**2、下载并配置Flup**  

2.1 从[Python官网的Flup页面](http://pypi.python.org/pypi/flup)下载最新的Flup，我下载的是[flup 1.0.2](http://pypi.python.org/packages/source/f/flup/flup-1.0.2.tar.gz#md5=24dad7edc5ada31dddd49456ee8d5254)  

2.2 解压后执行setup.py install就好了（依赖[setuptools](http://pypi.python.org/packages/2.7/s/setuptools/)）  

2.3 好了  


**3、启动web.py**  

Nginx已经启动了，Flup也已经安装好了，现在你需要的就是根据Nginx.conf中配置的fastcgi端口(我的配置文件中是8051)，启动你的web.py:  

> index.py 8051 fastcgi

顺利的话，你已经在Windows上搭好了一个web.py的Nginx Web服务器，Have fun :)

**参考文章**  

[Windows下Nginx + flup + fastcgi + webpy 开发应用环境的设置](http://blog.csdn.net/yavobo/article/details/6240122)  

[在windows上利用Nginx部署web.py](http://blog.ops.cc/develop/541ff02680c990b4.html)  
