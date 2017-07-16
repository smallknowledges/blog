---
layout: blog
istop: true
background-image: http://ot1cc1u9t.bkt.clouddn.com/17-7-16/78801196.jpg
title:  "nginx中server配置的共同部分抽取linux反向代理php"
date:   2017-06-20
category: nginx
tags:
- nginx
- server
- php
- linux
- 反向代理
---

 
如下所示:
```
server {
    listen       80;
    server_name  dev.drpb.com;
    root /Users/Stone/repo/oschina/drsoft/page-builder;
    location ~ \.php$ {
        fastcgi_index  index.php;
        fastcgi_pass  127.0.0.1:9000;
        include fastcgi.conf;
    }
}
server {
    listen       80;
    server_name  dev.drp.com;
    root /Users/Stone/repo/oschina/drsoft/site;
    location ~ \.php$ {
        fastcgi_index  index.php;
        fastcgi_pass  127.0.0.1:9000;
        include fastcgi.conf;
    }
}
```
在``nginx``配置中有两个``server``配置块, 它们有共同的``php``反向代理配置部分.
 
请问如何将:
 
```
location ~ \.php$ {
    fastcgi_index  index.php;
    fastcgi_pass  127.0.0.1:9000;
    include fastcgi.conf;
}
```
抽取出来放到一个地方，而不是在每个server中都写一次(我试着放到其共同父级的http块中是不可以的)，谢谢!

已找到答案：

将location部分抽取到一个外部文件，如命名为：common_rules.conf，放到nginx目录下。

修改server配置为:
 
```
server {
    server_name  dev.drpb.com;
    root /Users/Stone/repo/oschina/drsoft/page-builder;
    include common_rules.conf;
}
server {
    server_name  dev.drp.com;
    root /Users/Stone/repo/oschina/drsoft/site;
    include common_rules.conf;
}
```