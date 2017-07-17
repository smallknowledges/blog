---
layout: blog
title: "微信开发调试阶段浏览器缓存"
date: 2017-01-22
category: 微信
background-image: http://ot1cc1u9t.bkt.clouddn.com/17-7-16/13004205.jpg
tags:  
- 微信
- 浏览器缓存
- wekit
- no-cache
- Pragma
---
# 实现目的

微信开发调试阶段浏览器缓存／``html``标签中``meta``剔除``js``，``css``缓存</center>

## 解决方案
是在调试阶段或者频繁更新的页面加入以下头信息 ，防止页面被缓存的方法，在URL后面添加随机参数，这样每次访问的都是不同的连接 

### 加入以下头信息 

解决方案是在调试阶段或者频繁更新的页面加入以下头信息 

{% highlight css %}
 <meta http-equiv="Cache-Control" content="no-cache, no-store, must-revalidate" />  
<meta http-equiv="Pragma" content="no-cache" />  
<meta http-equiv="Expires" content="0" />  
   {% endhighlight %}

####  防止浏览器缓存

更新文件的时候，在引用css，js等文件的语句上加上一个版本号，就能有效防止浏览器一直使用缓存中的css，js  
     {% highlight css %}
     
<link href="css/demo.css?v=201606131149" rel="stylesheet">  

    {%endhighlight %}
#### 随机参数 

防止页面被缓存的方法，在URL后面添加随机参数，这样每次访问的都是不同的连接
 
{% highlight css %}

window.location='xxx.html?_r='+Math.random();  

    {% endhighlight %}

