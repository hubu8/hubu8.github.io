# 日常掉坑


<!--more-->

### nginx启动报错

```
docker run --name postNginx -v /work/html:/usr/share/nginx/html -v /work/nginx:/etc/nginx nginx
```

2021/05/19 14:46:05 [emerg] 1#1: open() "/etc/nginx/nginx.conf" failed (2: No such file or directory)
nginx: [emerg] open() "/etc/nginx/nginx.conf" failed (2: No such file or directory)

意思是没有找到/etc/nginx/nginx.conf文件，

第一种解决办法：

```shell
docker run --name=mynginx -p 80:80 -p 443:443 \
        --restart=always \
        -v /work/nginx/nginx.conf:/etc/nginx/nginx.conf \
    -v /work/nginx/conf.d:/etc/nginx/conf.d \
    -v /work/nginx/www:/usr/share/nginx/html \
    -v /work/nginx/logs:/var/log/nginx -v /work/nginx/cart:/etc/nginx/cart \
    -d nginx
```

第二种解决办法：

​	都提示了是没有权限造成的，那就排查下权限。看看宿主机本地的`nginx.conf`对`other`用户是否有读权限，再排查下该配置文件的所有父级目录是否有访问权限。或者像一楼说的用`docker run --privileged=true`启动容器，这样就是使用`root`用户权限了。



### nginx代理转发错误

```shell
 connect() failed (111: Connection refused) while connecting to upstream, client: 202.114.144.231, server: 111.47.28.118, request: "POST /prd-api/authorization-server/oauth/token?username=admin&password=password&grant_type=password&scope=read HTTP/1.1", upstream: "http://111.47.28.118:7181/authorization-server/oauth/token?username=admin&password=password&grant_type=password&scope=read", host: "111.47.28.118:7191", referrer: "http://111.47.28.118:7191/"
 
202.114.144.231 - test_client [10/Jun/2021:09:53:55 +0000] "POST /prd-api/authorization-server/oauth/token?username=admin&password=password&grant_type=password&scope=read HTTP/1.1" 502 494 "http://111.47.28.118:7191/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.77 Safari/537.36 Edg/91.0.864.41" "-"

```

目前nginx配置文件

```conf
server {
    listen       80;
    listen  [::]:80;
    server_name  111.47.28.118;

    #access_log  /var/log/nginx/host.access.log  main;

   
    location / {
        root   /usr/share/nginx/html/AAMS;
        index  index.html index.htm;
    }
    
    location /prd-api {
        rewrite  ^/prd-api/(.*)$ /$1 break;
        proxy_pass  http://111.47.28.118:7181;
        proxy_set_header Authorization $http_authorization;
    }
 
    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

}

```

nginx.conf配置：

```
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;
    underscores_in_headers on;
    add_header Access-Control-Allow-Headers Authorization;

    include /etc/nginx/conf.d/*.conf;
}
```

#### 解决：

