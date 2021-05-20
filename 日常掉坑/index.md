# 日常掉坑


<!--more-->

```
docker run --name postNginx -v /work/html:/usr/share/nginx/html -v /work/nginx:/etc/nginx nginx
```

2021/05/19 14:46:05 [emerg] 1#1: open() "/etc/nginx/nginx.conf" failed (2: No such file or directory)
nginx: [emerg] open() "/etc/nginx/nginx.conf" failed (2: No such file or directory)

```shell
docker run --name=mynginx -p 80:80 -p 443:443 \
        --restart=always \
        -v /work/nginx/nginx.conf:/etc/nginx/nginx.conf \
    -v /work/nginx/conf.d:/etc/nginx/conf.d \
    -v /work/nginx/www:/usr/share/nginx/html \
    -v /work/nginx/logs:/var/log/nginx -v /work/nginx/cart:/etc/nginx/cart \
    -d nginx
```


