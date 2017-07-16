---
layout: blog
title:  "web爬虫之识别PhantomJs识别"
date:   2016-06-14 22:54:32
category: PhantomJs
background-image: http://ot1cc1u9t.bkt.clouddn.com/17-7-16/93730624.jpg
tags: 
- PhantomJs识别
- PhantomJs
- web
- web爬虫
- 爬虫
- PhantomJs爬虫
---
  

如今web爬虫技术日趋成熟,国内大大小小的技术团队都开始尝试PhantomJs.
 
现在的``web``技术领域,客户端js大行其道,页面异步加载信息已成为家常便饭,用PhantomJs做爬虫的一大好处是解决了前端异步加载的资源无法被爬虫抓取的问题.
 
然而这也给很多具有版权保护问题的网站带来了困扰,强大的PhantomJs让野爬机器人如虎添翼, 如果说之前还能通过ajax异步加载信息保护自己的页面内容不被野爬轻易抄袭转载的话,现如今一旦遇到PhantomJs就束手无策了.
 
无论是小说网站,新闻网站,亦或是个人博客都面临着被PhantomJs爬虫非法抓取和抄袭转载的风险,那么PhantomJs客户端能否被我们识别出来呢?

首先,在服务端收到http请求时,PhantomJs发出的请求就具有明显的特征,我们先来看看一个

- 普通的http请求:
 
![普通的http请求](http://www.liberxue.com/images/b8-p1.png)

再来看看同样情况下服务端收到的

- ``PhantomJs``客户端发出的``http请求``:

![PhantomJs客户端发出的http请求](http://www.liberxue.com/images/b8-p2.png)

 
##### 仔细观察对比不难发现,PhantomJs具有如下特征:
 
1. 请求头中的``Host``是位于最后的;
  
2. ``Connection``字段的每个单词首字母都是大写的(``Keep-Alive``),而正常的浏览器客户端只有第一个单词是首字母大写(Keep-alive);
 
3. ``Accept-Encoding``的值是``gzip``
 
4. ``User-Agent``里面包含有``PhantomJs``的签名,不过这一条``PhantomJs``客户端是可以修改的.

 
前三条特征之所以会存在,主要的原因是PhantomJs是用著名的C++框架Qt开发的,而Qt框架的网络栈的实现具有以上的特征.
 
通过以上几点特征,我们可以在服务端controller/action层对客户端情况做一个初步的检测,把PhantomJs客户端的http请求拒绝掉.
 
然后再说说在客户端js代码中如何检测PhantomJs:
 
因为``userAgent``字段是可以修改的,所以对``ua``的判断我们就不说了,说点修改成本高或者根本不能修改的PhantomJs客户端特征.
 
首先是``Plugins``,对于浏览器中``navigator.plugins``对象来说,一般至少都会包含以下值:``Flash``,``ActiveX``, ``Java applets``支持以及一个叫做"``Default Browser Helper``",这是一个在OSX系统中用来判断当前浏览器是否被系统设为默认浏览器的插件. 多数正常安装的浏览器客户端都会至少包含以上插件中的一到两个,即使是移动端浏览器.
 
不同的是,在PhantomJs客户端中是没有任何plugin的,并且它目前的官方文档中也没有提供任何添加plugin的方法.因此对于plugin方面PhantomJs客户端 要把自己伪装成和正常浏览器一模一样的特征成本较高.

##### js可以放在客户端脚本中对PhantomJs进行判断:

![客户端脚本中对PhantomJs进行判断](http://www.liberxue.com/images/b8-p3.png)

然而``PhantomJs``如果一定要在``plugin``方面模仿一个正常的浏览器也不是不可能,因为Qt框架也是提供了一套``native Api``来实现``plugin``的, 真要做的话也只是一个开发成本的问题.

另外的一个可以作为判断客户端的思路是利用alert弹窗的用户响应时间进行判断,来看看下面这段代码:

![alert弹窗的用户响应时间进行判断](http://www.liberxue.com/images/b8-p3.png)
 
另外,``PhantomJs``客户端中有两个全局变量,``window.callPhantom``和``window._phantom``,但有消息表明这两个值在未来很可能会发生改变.
 
``PhantomJs``还有一个弱点是,由于1.x和2.x版本的Phantom使用了较为过时的WebKit引擎,例如``PhantomJs 1.x``版本中``Function.prototype.bind``是不存在的, 但依据这一点不足以判断PhantomJs客户端,因为这很可能会误伤使用者低版本IE浏览器的正常用户

除此之外``PhantomJs``的``Error``异常捕获方面也有些文章可做,不过我认为这方面同样有可能导致误伤正常用户,毕竟我不能确定所有正常的浏览器都不具备这样的特征.










