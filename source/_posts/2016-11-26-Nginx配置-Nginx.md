---
layout: post
date: 2016-11-26
title: Nginx配置
categories: Nginx
---

Nginx能够实现反向代理，负载均衡，静态文件提供等等，是一个强大的Web服务器。

在我网络信息安全中，写了个大作业，Google Authenticator的服务端实现，使用了Nginx代理，Https，密码加盐哈希。

这里是GitHub的地址：https://github.com/Rayooo/GoogleAuthenticatorNode

以下是nginx.conf配置文件



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

	    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
	    #                  '$status $body_bytes_sent "$http_referer" '
	    #                  '"$http_user_agent" "$http_x_forwarded_for"';

	    #access_log  logs/access.log  main;

	    sendfile        on;
	    #tcp_nopush     on;

	    #keepalive_timeout  0;
	    keepalive_timeout  65;

	    #gzip  on;

	    # 这个将为打开文件指定缓存，默认是没有启用的，max 指定缓存数量，
	    # 建议和打开文件数一致，inactive 是指经过多长时间文件没被请求后删除缓存。
	    open_file_cache max=1000 inactive=20s;

	    # open_file_cache 指令中的inactive 参数时间内文件的最少使用次数，
	    # 如果超过这个数字，文件描述符一直是在缓存中打开的，如上例，如果有一个
	    # 文件在inactive 时间内一次没被使用，它将被移除。
	    open_file_cache_min_uses 2;

	    # 这个是指多长时间检查一次缓存的有效信息
	    open_file_cache_valid 30s;


	    upstream myServer {
			ip_hash
	        server 127.0.0.1:3001 weight=2; ;
	        server 127.0.0.1:3002;
	        # server 127.0.0.1:3003 backup;
	    }

		# HTTP server

		server {
		   listen 80;
		   return 301 https://$host$request_uri;
		}

	    # HTTPS server
	   
	    server {
	       listen       443 ssl;
	       server_name  localhost;

	       ssl_certificate      /home/ubuntu/GoogleAuthenticatorNode/bin/server.crt;
	       ssl_certificate_key  /home/ubuntu/GoogleAuthenticatorNode/bin/key.pem;

	       ssl_session_cache    shared:SSL:1m;
	       ssl_session_timeout  5m;

	       ssl_ciphers  HIGH:!aNULL:!MD5;
	       ssl_prefer_server_ciphers  on;

	       location / {
	 			proxy_set_header        Host $host;
	      		proxy_set_header        X-Real-IP $remote_addr;
	      		proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
	      		proxy_set_header        X-Forwarded-Proto $scheme;
	            proxy_pass         https://myServer;
	       }

	       location ~ ^/(assets|images|js|stylesheets|userHomePageAssets)/{
            	root /home/ubuntu/GoogleAuthenticatorNode/public;
       	   }
	    }
	    include servers/*;
	}



负载均衡
---

重点是upstream这里的配置，ip_hash可以使得某个ip的请求定向到同一台后端，能建立起稳固的session，

在需要使用负载的Server节点下添加  proxy_pass http://myServer;

upstream 每个设备的状态:

	down 表示单前的server暂时不参与负载 
	weight  默认为1.weight越大，负载的权重就越大。 
	max_fails ：允许请求失败的次数默认为1.当超过最大次数时，返回proxy_next_upstream 模块定义的错误 
	fail_timeout:max_fails 次失败后，暂停的时间。 
	backup： 其它所有的非backup机器down或者忙的时候，请求backup机器。所以这台机器压力会最轻。


location 匹配规则
---

语法  location [=|~|~*|^~] /uri/ { … }

模式	含义

location = /uri	  =表示精确匹配，只有完全匹配上才能生效

location ^~ /uri	^~ 开头对URL路径进行前缀匹配，并且在正则之前。

location ~ pattern	 开头表示区分大小写的正则匹配

location ~* pattern	 开头表示不区分大小写的正则匹配

location /uri	不带任何修饰符，也表示前缀匹配，但是在正则匹配之后

location /	通用匹配，任何未匹配到其它location的请求都会匹配到，相当于switch中的default


多个 location配置的情况下匹配顺序为

首先精确匹配 =

其次前缀匹配 ^~

其次是按文件中顺序的正则匹配

然后匹配不带任何修饰的前缀匹配。

最后是交给 / 通用匹配

当有匹配成功时候，停止匹配，按当前匹配规则处理请求


	# 直接匹配网站根，通过域名访问网站首页比较频繁，使用这个会加速处理，官网如是说。
	# 这里是直接转发给后端应用服务器了，也可以是一个静态首页
	# 第一个必选规则
	location = / {
	    proxy_pass http://tomcat:8080/index
	}

	# 第二个必选规则是处理静态文件请求，这是 nginx 作为 http 服务器的强项
	# 有两种配置模式，目录匹配或后缀匹配，任选其一或搭配使用
	location ^~ /static/ {
	    root /webroot/static/;
	}
	location ~* \.(gif|jpg|jpeg|png|css|js|ico)$ {
	    root /webroot/res/;
	}

	# 第三个规则就是通用规则，用来转发动态请求到后端应用服务器
	# 非静态文件请求就默认是动态请求，自己根据实际把握
	# 毕竟目前的一些框架的流行，带.php、.jsp后缀的情况很少了
	location / {
	    proxy_pass http://tomcat:8080/
	}

静态文件服务
---

最简单的本地静态文件服务配置示例

	server {
	        listen       80;
	        server_name www.test.com;
	        charset utf-8;
	        root   /data/www.test.com;
	        index  index.html index.htm;
	       }


Nginx单单设置静态文件服务并没有发挥真正的功力，真正需要的是文件缓存

	http {
	    # 这个将为打开文件指定缓存，默认是没有启用的，max 指定缓存数量，
	    # 建议和打开文件数一致，inactive 是指经过多长时间文件没被请求后删除缓存。
	    open_file_cache max=204800 inactive=20s;

	    # open_file_cache 指令中的inactive 参数时间内文件的最少使用次数，
	    # 如果超过这个数字，文件描述符一直是在缓存中打开的，如上例，如果有一个
	    # 文件在inactive 时间内一次没被使用，它将被移除。
	    open_file_cache_min_uses 1;

	    # 这个是指多长时间检查一次缓存的有效信息
	    open_file_cache_valid 30s;

	    # 默认情况下，Nginx的gzip压缩是关闭的， gzip压缩功能就是可以让你节省不
	    # 少带宽，但是会增加服务器CPU的开销哦，Nginx默认只对text/html进行压缩 ，
	    # 如果要对html之外的内容进行压缩传输，我们需要手动来设置。
	    gzip on;
	    gzip_min_length 1k;
	    gzip_buffers 4 16k;
	    gzip_http_version 1.0;
	    gzip_comp_level 2;
	    gzip_types text/plain application/x-javascript text/css application/xml;

	    server {
	            listen       80;
	            server_name www.test.com;
	            charset utf-8;
	            root   /data/www.test.com;
	            index  index.html index.htm;
	           }
	}


Angel的静态文件服务，仅提供一个前端的文件代理
---

主要是include mime.types，之前是因为这个坑而导致css加载不全

	worker_processes  1;
	events { worker_connections  1024; }
	http {
	    server {
	      root /Users/Ray/Downloads/backend-angular;  
	      listen 8080;

	      location / {
	        include  /usr/local/etc/nginx/mime.types;
	      }
	    }

	}


