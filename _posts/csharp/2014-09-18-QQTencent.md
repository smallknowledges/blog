---
layout: blog
title: "c#实现点击网页启动QQ,Tencent://Message/等协议的实现原理"
background: green
date:  2016-09-22
category: csharp
background-image: http://ot1cc1u9t.bkt.clouddn.com/17-7-16/43490780.jpg
tags:
- tencent
- HKEY_CLASSES_ROOT
- 网页启动QQ
- chrome
- chromium
---
 
腾讯官方通过 Tencent://Message/协议可以让QQ用户显示QQ/TM的在线状态发布在互联网上；并且点击 ，不用加好友也可以聊天：
##### 具体代码：

 ```
 <a href="tencent://message/?uin=363946651&Site=oicqzone.com.com&Menu=yes"> 
<img border="0" SRC='http://wpa.qq.com/pa?p=1:363946651:3' alt="点击这里给我发消息"> 
</a> 
 ```

 但它是如何实现的呢？下面文章以及微软官方说明详细解释了其工作原理：
 
微软官方说明：http://msdn.microsoft.com/library/default.asp?url=/workshop/networking/pluggable/overview/appendix_a.asp
Register protocol，此文中对于 Windows、Linux 和 OS X 操作系统如何注册协议都有说明。比如说 Windows，其实只需写入注册表，即可实现协议与执行程序的关联。例如腾讯的Tencent://Message协议注册表如下：

```
[HKEY_CLASSES_ROOT\TENCENT] 
@=”TencentProtocol” 
“URL Protocol”=”D:\\Program Files\\Tencent\\QQ\\liberxue.exe” 
 
[HKEY_CLASSES_ROOT\TENCENT\DefaultIcon] 
@=”D:\\Program Files\\Tencent\\QQ\\liberxue.exe,1″ 
 
[HKEY_CLASSES_ROOT\TENCENT\shell] 
 
[HKEY_CLASSES_ROOT\TENCENT\shell\open] 
 
[HKEY_CLASSES_ROOT\TENCENT\shell\open\command] 
@=”\”D:\\Program Files\\Tencent\\QQ\\liberxue.exe\” \”%1\”" 

```

此注册表所实现的就是当浏览器（或其它）碰到 tencent://… 时，自动调用 liberxue.exe，并把 tencent://… 地址作为第一个参数传递给 liberxue.exe。
 
##### 附程序代码： 
 
在``Windows``中，协议注册由操作系统（OS）进行。例如，当您设置一个Web浏览器作为默认浏览器，操作系统将注册HTTP， HTTPS和FTP协议，并把它们与Web浏览器相关联; 当你打开一个``HTTP HTTPS``或``FTP``的邮件或从``Internet``快捷方式的链接，该URL将在您的默认浏览器中打开。同样，当你设置一个默认的邮件客户端，操作系统注册了的mailto协议，这样的mailto在浏览器中的相关邮件应用程序中打开链接。
注： ``Mozilla``套件/ ``SeaMonkey``的集成邮件和浏览器功能，所以在一封电子邮件中说网络链接（例如，HTTP：//链接）将在``SeaMonkey``的浏览器窗口中打开和“电子邮件地址：”在网页中的链接将在SeaMonkey的邮件打开，且无需考虑操作系统的协议处理程序。
其它协议可以注册并通过配置已安装的程序来处理的内容的外部应用程序相关联。例如，你可以设置RealPlayer中充当注册协议处理程序的RTSP URL的（实时流协议，以「内容- >媒体类型“下的RealPlayer的偏好设置）。当你下次打开 ``rtsp:// link``，``Firefox``或``Mozilla``套件/ ``SeaMonkey``的会要求您确认外部协议请求之前，它会启动应用程序。
也可以通过创建，如下.reg文件等，并在文件中把这个注册协议：

```
REGEDIT4 
 
[HKEY_CLASSES_ROOT\foo] 
@="URL:foo Protocol" 
"URL Protocol"="" 
 
[HKEY_CLASSES_ROOT\foo\shell] 
 
[HKEY_CLASSES_ROOT\foo\shell\open] 
 
[HKEY_CLASSES_ROOT\foo\shell\open\command] 
@="\"C:\\Program Files\\Application\\program.exe\" \"%1\""  

```

更换与你指定的协议的C:\\Program Files\\Application\\program.exe和你要运行的程序。需要注意的是反斜杠必须加倍和路径前附加斜线和报价不能除去。保存文件，然后双击它插入到Windows注册表。请参见注册应用程序到一个URL协议以获取更多信息。
 
##### Linux所有版本的Firefox（需要安装特定的GNOME库）
在终端中键入：

```
gconftool-2 -s /desktop/gnome/url-handlers/foo/command '/path/to/app %s' --type String 
gconftool-2 -s /desktop/gnome/url-handlers/foo/enabled --type Boolean true 

```

更换美孚在两条线上与您要注册的协议/路径/到/应用程序的路径，你要运行的应用程序。

 

##### Firefox 3.5及以上

（工作时无需安装GNOME库）

输入``about：config``警告到地址栏（地址栏），然后按Enter键。

用鼠标右键单击- >新建- >布尔- >名称：network.protocol-handler.expose.foo - >价值- > 假（更换美孚与你指定的协议）

下一次当你点击的协议类型为foo，你会被要求的应用程序与打开一个链接。

 

Firefox的版本高达3.0

（工作时无需安装GNOME库）

键入about：配置到地址栏（地址栏），然后按Enter键。

用鼠标右键单击- >新建- >布尔- >名称：network.protocol-handler.external.foo - >价值- > 真（更换美孚与你指定的协议）

用鼠标右键单击- >新建- >字符串- >名称：network.protocol-handler.app.foo - >价值- > /路径/到/应用程序（更换美孚的协议你指定和/路径/到/应用程序与路径的应用要运行）

确保network.protocol-handler.expose，都被设置为true。

MAC  Firefox的版本最高3.0：

键入about：配置到地址栏，然后按Enter键。

用鼠标右键单击- >新建- >布尔- >名称：network.protocol-handler.external.foo - >价值- > 真（更换美孚与你指定的协议）

用鼠标右键单击- >新建- >字符串- >名称：network.protocol-handler.app.foo - >价值- > /路径/到/应用程序（更换美孚的协议你指定和/路径/到/应用程序与路径，你要运行的应用程序。

确保network.protocol-handler.expose，都被设置为真。

 

##### 故障排除

 

（Linux和Mac），如果设置时的路径或名称不正确network.protocol-handler.app。（协议）的偏好，Firefox会显示一个错误说“协议（富）未与任何程序关联”。（错误312953）

如果您收到一条消息，在Firefox的的mailto协议，不与任何程序关联，重新设置您的默认邮件客户端。

某些不安全的协议，在默认情况下（忽略的bug 173010）。

 