---
title: nginx调试思路
date: 2018-04-01 14:58:37
tags: 
	- nginx
categories: 
	- nginx
---

# 调试nginx匹配规则的一个思路
**思路简介：通过rewrite方法，将匹配结果rewrite为指定内容，根据最终结果达到日志输出的效果**

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

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       9089;
        server_name  localhost;
	# root /usr/local/html;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

	#location / {
	#	root /usr/local/html;
	#}

	location = /abc {
		root /usr/local/html;
		rewrite '^/[a-z].*' /a;
		break;
	}

	location /abc {
		root /usr/local/html;
		rewrite '^/[a-z].*' /b;
		break;
	}

	location /abcd {
		root /usr/local/html;
		rewrite '^/[a-z].*' /c;
		break;
	}

	location ^~ /abcde {
		root /usr/local/html;
		rewrite '^/[a-z].*' /d;
		break;
	}

	location ~ /abcdef {
		root /usr/local/html;
		rewrite '^/[a-z].*' /e;
		break;
	}

	location ~* /abcdefg {
		root /usr/local/html;
		rewrite '^/[a-z].*' /f;
		break;
	}

	location ~* /abcdf {
		root /usr/local/html;
		rewrite '^/[a-z].*' /g;
		break;
	}

	location ~ /abcdfgh {
		root /usr/local/html;
		rewrite '^/[a-z].*' /h;
		break;
	}

	location ~ /abcdgh {
		root /usr/local/html;
		rewrite '^/[a-z].*' /i;
		break;
	}

	location ~* /abcdgh {
		root /usr/local/html;
		rewrite '^/[a-z].*' /j;
		break;
	}


	location ~* \.txt {
            root   /usr/local/html;
        }

	location ^~ /ffhh {
		root /usr/local/html;
		rewrite '^/[fh].*' /k;
		break;
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
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
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

}
```

结果页a的内容：
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>welcome</title>
</head>
<body>
<h1>----------------- a -----------------</h1>
<h1>hello world ! where are you from ?</h1>
</body>
</html>
```

结果页b的内容：
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>welcome</title>
</head>
<body>
<h1>----------------- b -----------------</h1>
<h1>hello world ! where are you from ?</h1>
</body>
</html>
```

其他c、d、e等结果页的内容类似

对以上配置的执行结果示例：
```
curl localhost:9089/abc   	  // a
curl localhost:9089/abcll  	  // b
curl localhost:9089/abcdll 	  // c
curl localhost:9089/abcde  	  // d
curl localhost:9089/abcdef 	  // d
curl localhost:9089/abcdefll  // d
页面e、f是不可能匹配到的了，因为都被d拦截了
curl localhost:9089/abcdf     // g
curl localhost:9089/abcdfgh   // g
页面h也是不可能匹配到的了，因为都被g拦截了
curl localhost:9089/abcdgh    // i
页面j也是不可能匹配到的了，因为都被i拦截了
```

附录：
以下内容引用至nginx[官方文档](http://nginx.org/en/docs/http/ngx_http_core_module.html#location)

> A location can either be defined by a prefix string, or by a regular expression. Regular expressions are specified with the preceding “~*” modifier (for case-insensitive matching), or the “~” modifier (for case-sensitive matching). To find location matching a given request, nginx first checks locations defined using the prefix strings (prefix locations). Among them, the location with the longest matching prefix is selected and remembered. Then regular expressions are checked, in the order of their appearance in the configuration file. The search of regular expressions terminates on the first match, and the corresponding configuration is used. If no match with a regular expression is found then the configuration of the prefix location remembered earlier is used.

> location blocks can be nested, with some exceptions mentioned below.

> For case-insensitive operating systems such as macOS and Cygwin, matching with prefix strings ignores a case (0.7.7). However, comparison is limited to one-byte locales.

> Regular expressions can contain captures (0.7.40) that can later be used in other directives.

> If the longest matching prefix location has the “^~” modifier then regular expressions are not checked.

> Also, using the “=” modifier it is possible to define an exact match of URI and location. If an exact match is found, the search terminates. For example, if a “/” request happens frequently, defining “location = /” will speed up the processing of these requests, as search terminates right after the first comparison. Such a location cannot obviously contain nested locations.

翻译总结一下location的匹配规则：
> location的匹配方式可以通过字符串前缀或正则表达式的形式表示。当接收到一个请求时，nginx将按以下步骤来选择location：
> 1、nginx先按字符串前缀的形式查找匹配的location，选中并记住匹配度最长的那个location。
> 2、如果被选中的location使用了“=”修饰符，则将当前选中结果作为最终结果，停止继续查找；
> 3、如果被选中的location使用了“^~”修饰符，则将当前选中结果作为最终结果，停止继续查找；
> 4、如果被选中的location未使用“=”或“^~”修饰符，则继续查找，执行步骤5；
> 5、然后，再按照正则的定义顺序查找匹配的location，选中第一个符合要求的正则location。
