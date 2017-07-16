---
layout: blog
title: "公布了x讯管家漏洞QQ永久封号"
date: 2017-03-07
category: vc
background-image: https://o243f9mnq.qnssl.com/2017/03/749634581.jpg
tags:
- nasm
- vc
- 硬盘锁
---
 

##vc和汇编（nasm）编写的硬盘锁，
主要原理是在电脑开机自检主板内存进入硬盘时间对windows启动的删除和分区表硬盘MBR异或加密，请勿用于非法勒索谢谢， 可以绕过国内的腾讯管家 360 杀毒软件等

##VChdlocker
##### vc和汇编（nasm）编写的硬盘锁
###### 主要原理：
>在电脑开机自检`主板``内存`进入`硬盘`时间对windows启动的删除和分区表硬盘`MBR`异或加密

<figure>
	<a href="http://farm9.staticflickr.com/8426/7758832526_cc8f681e48_b.jpg"><img src="http://farm9.staticflickr.com/8426/7758832526_cc8f681e48_c.jpg"></a>
	<figcaption><a href="http://www.flickr.com/photos/80901381@N04/7758832526/" title="Morning Fog Emerging From Trees by A Guy Taking Pictures, on Flickr">邮箱，第三方账号都进不去了，空间2万对多照片丢失</a>.</figcaption>
</figure>

# 请勿用于非法勒索谢谢
#此代码和原理在网上学习不少大神和方法，找不到署名，再次表示感谢。

注释说明：
---
- [x] 表示项目必须运行中必须要使用的   
- [ ] 表示项目在debug重生成的

#### **开发工具Visual Studio 2015 vc sdk **

文件目录
----
- [x] Config.ini
- [ ] Debug
- [ ] LICENSE
- [ ] README.md
- [x] bin.h
- [ ] hi013.VC.db
- [ ] hi013.sln
- [ ] hi013.vcxproj
- [ ] hi013.vcxproj.filters
- [x] main.cpp
- [x] nasm
- [x] 解锁密码为CaM加上Config.ini中的psw的值


文件名  | 备注说明 
---|---
Config.ini | 解锁密码为CaM加上Config.ini中的psw的值
Debug | 生成调试
bin.h | nasm二进制文件copy进去
main.cpp | vc运行包含异或加密等主文件
nasm | nasm编译工具

------
nasm 使用方法
----


```
nasm.exe bin.asm -o bin
```
#### 截图

<figure class="half">
    <a href="/upload-img/WX20170309-083646@2x.png"><img src="/upload-img/qq.jpeg"></a>
    <a href=""><img src="/upload-img/VChdlocker.png"></a>
    <figcaption>截图留个纪念吧，做人真的不能太得瑟了</figcaption>
</figure>


```
sequenceDiagram
我->>x: 生日快乐?
x->>我: 丢了Q也何尝不是最好的了断!
```
