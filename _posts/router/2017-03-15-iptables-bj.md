---
layout: blog
title:  "iptables防火墙使用笔记"
date:   2017-03-15
category: router
background-image: http://ot1cc1u9t.bkt.clouddn.com/17-7-16/30065910.jpg
tags:
- iptables
- freebsd
- centos7
- udp
- ipu
- eth
---

**一、背景**

每次WEB项目上线时总是要安装环境，其中免不了配置防火墙来允许外网访问。之前都是网上找教程比葫芦画瓢把开启80端口命令敲一下就不管了。本着学习的精神，今天打算好好研究一下iptables配置，并作笔记以便之后查看。只求明白即可，具体深入原理啥的就算了。

注：centos7默认防火墙改为firewall而非iptables。

**二、快速搞定**

iptables -I INPUT -p tcp --dport 80 -j ACCEPT
如果不想往下看的话，基本上只需记下这条命令就可以开启80端口，想开其他端口只需要把80换成需要就行（比如3306）。但如果还是不能解决问题，那就得好好看看规则定义了。

**三、什么是iptables**

iptables就是用规则列表的形式实现网络访问控制，但与防火墙是两个概念。虽然网上都这么叫。

**四、怎么看iptables规则** 

iptables -L
附加子命令
 -n：以数字的方式显示ip，它会将ip直接显示出来，如果不加-n，则会将ip反向解析成主机名。
 -v：显示详细信息
一个规则列表例子
{% highlight shell %}
[root@sstest ~]# iptables -L
Chain INPUT (policy ACCEPT)
target     prot opt source               destination         
ACCEPT     tcp  --  anywhere             anywhere            tcp dpt:http 
ACCEPT     all  --  anywhere             anywhere            state RELATED,ESTABLISHED 
ACCEPT     icmp --  anywhere             anywhere            
ACCEPT     all  --  anywhere             anywhere            
ACCEPT     tcp  --  anywhere             anywhere            state NEW tcp dpt:ssh 
REJECT     all  --  anywhere             anywhere            reject-with icmp-host-prohibited 

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination         
REJECT     all  --  anywhere             anywhere            reject-with icmp-host-prohibited 

Chain OUTPUT (policy ACCEPT)
target     prot opt source  
}
{% endhighlight %}
**destination** 
1、iptables有三个规则链（chain）。分别为INPUT（数据包流入口） ，FORWARD （转发），OUTPUT（数据包流出口）。在定义任何规则时基本上都要写明是哪个。

2、iptables有两种策略，一种叫“通”策略，一种叫“堵”策略，通策略，默认门是关着的，必须要定义谁能进。堵策略则是，大门是洞开的，但是你必须有身份认证，否则不能进。上面例子中(policy ACCEPT)即表示策略默认全部可以通过，同理相反的还有DROP。

**修改策略命令是**
```
iptables -P chain (DROP|ACCEPT)
```
 3、规则的次序非常关键，谁的规则越严格，应该放的越靠前，而检查规则的时候，是按照从上往下的方式进行检查的。所以一定要注意添加顺序问题。基本上规则无效多半是因为这种情况。

**五、怎么添加删除iptables规则 ** 

首先，先是增删改的命令。上文说到规则次序问题，所以就有两个添加参数啦

`-A：追加，在当前链的最后新增一个规则
-I num : 插入，把当前规则插入为第几条。
-I 3 :插入为第三条
-R num：Replays替换/修改第几条规则
格式：iptables -R 3 …………
-D num：删除，明确指定删除第几条规则`

 
>然后呢，就要添加匹配标准了。

` -s：指定作为源地址匹配，这里不能指定主机名称，必须是IP。IP | IP/MASK | 0.0.0.0/0.0.0.0 而且地址可以取反，加一个“!”表示除了哪个IP之外 
 -d：表示匹配目标地址 
 -p：用于匹配协议的（这里的协议通常有3种，TCP/UDP/ICMP） 
 -i eth0：从这块网卡流入的数据 
流入一般用在INPUT和PREROUTING上 
 -o eth0：从这块网卡流出的数据 
`
流出一般在OUTPUT和POSTROUTING上
 其中，协议匹配（-p）有一些隐含参数
```
-p tcp :

--dport XX-XX：指定目标端口,不能指定多个非连续端口,只能指定单个端口
```
--sport：指定源端口
--tcp-fiags：TCP的标志位（SYN,ACK，FIN,PSH，RST,URG）

-p udp：
--dport
--sport

-p icmp：
--icmp-type：
echo-request(请求回显)，一般用8 来表示
```

所以 --icmp-type 8 匹配请求回显数据包
```
echo-reply （响应的数据包）一般用0来表示
```
 

 匹配标准有了，最后就是执行动作了（ACTION）

` -j ACTION：常用的ACTION有三种DROP（悄悄丢弃）；REJECT（明示拒绝）；ACCEPT（接受） `

 

了解了这些命令参数之后，前文中添加80端口的命令的含义就一目了然了(⊙▽⊙)

**六、总结**

　　iptables是一个非常重要的工具，几乎是每一台服务器所必备设置的。当然，iptables的功能远不止这些。本文只是起到抛砖引玉的作用，要想了解更多关于linux防火墙设置还是要多看看网上其他技术大牛的文章


