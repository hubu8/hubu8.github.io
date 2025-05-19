# https站点无法使用http资源


<!--more-->

### 问题描述

部署WEB项目后，开启了强制HTTPS，产生如下错误：

```
hubu8.github.io/:1  Mixed Content: The page at 'https://hubu8.github.io/friend/' was loaded over HTTPS, but requested an insecure image 'http://www.ruoyi.vip/images/ruoyi_vue.png'. This request has been blocked; the content must be served over HTTPS.
```

![image-20230913165951398.png](/posts/前端/https站点无法使用http资源/image-20230913165951398.png)

### 问题分析

报错的原因就是当前页面是https协议加载的，但是这个页面发起了一个http的ajax请求，这种做法是非法的。HTTPS页面里动态的引入HTTP资源，比如引入一个js文件，会被直接block掉的.在HTTPS页面里通过AJAX的方式请求HTTP资源，也会被直接block掉的。

### 解决办法

可以在相应的页面的里加上这句代码，意思是自动将http的不安全请求升级为https

```
<meta http-equiv="Content-Security-Policy" content="upgrade-insecure-requests">
```


