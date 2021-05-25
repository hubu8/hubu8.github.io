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




