---
layout: blog
title: "源码编译cef3(Chromium) 在Windows上从加入mp3支持"
date:  2016-09-22
background-image: http://ot1cc1u9t.bkt.clouddn.com/17-7-16/2801546.jpg
category: csharp
tags:
- chromiumfx
- ffmpeg
- cef3 
- chrome
- chromium
- webkit
---
 

<center><b>webkit</b>在使用<kbd>chromiumfx</kbd>+<kbd>cef3</kbd>过程中发现不支持主流的<kbd>mp3</kbd>、<kbd>mp4</kbd>格式，经过搜索发现因为这些格式不是开放格式，出于版权的考虑在<kbd>chromium</kbd>编译配置中去掉了对这些格式的支持。要添加支持必须修改编译的配置参数。网上流传的通过复制<kbd>chrome</kbd>目录下<kbd>ffmpeg.dll</kbd>替换的方式经过实践并没有成功，于是决定动手编译</center>
***
#一、什么是CEF

CEF即<kbd>Chromium Embeded Framework</kbd>，由谷歌的开源浏览器项目Chromium扩展而来，可方便地嵌入其它程序中以得到浏览器功能。

CEF包括CEF1和CEF3两个版本，CEF1已停止更新。由于CEF只支持C、C++，所以出现了很多第三方项目来提供其它语言的支持。

.NET 的包装类库有<kbd>chromiumfx</kbd>、<kbd>cefglue</kbd>、<kbd>cefsharp</kbd>，其它语言参看https://bitbucket.org/chromiumembedded/cef#markdown-header-external-projects 。

CEF版本号说明：<kbd>3.2357.1281.gd660177<kbd> CEF版本号.Branch.Patch.Hash</kbd>

#二、为何要编译CEF

在使用chromiumfx+cef过程中发现不支持主流的mp3、mp4格式，经过搜索发现因为这些格式不是开放格式，出于版权的考虑在chromium编译配置中去掉了对这些格式的支持。要添加支持必须修改编译的配置参数。网上流传的通过复制chrome目录下ffmpeg.dll替换的方式经过实践并没有成功，于是决定动手编译。

#三、编译先决条件

1.快速稳定的<kbd>VPN</kbd> (不但要fq而且要稳定。下载webkit时候单个文件4G+，一旦失败则需要重新下载

2.64位操作系统，win7+

3.硬盘空间40G+，内存4G+

4.大量的时间 （下载五六个小时，编译两三个小时

5.vs2013 update4、win8.1 SDK (Express不行，update5+未测试

#四、编译过程

1.升级vs13至update4，安装Microsoft Foundation Classes for C++（MFC库，可运行vs安装包安装），安装win8.1 SDK。

2.设置系统区域为英语（美国）。（控制面板-区域-管理-更改系统区域设置-英语（美国）

3.添加环境变量
```
DEPOT_TOOLS_WIN_TOOLCHAIN=0

GYP_GENERATORS=ninja,msvs-ninja

GYP_MSVS_VERSION=2013
```
4.创建工作目录，尽量简单，不带空格特殊字符。例：e:\ws

5.下载解压工具包解压至工作目录，并添加到系统环境变量Path末尾。例：e:\ws\depot_tools

下载：depot_tools （原页面

6.下载编译脚本至工作目录

下载：automate-git.py 

7.工作目录下建立源码目录。例：e:\ws\source

8.以管理员身份运行cmd，切换至工作目录，运行gclient
```
1 e:
2 cd e:\ws
3 gclient
```
8.以管理员身份运行cmd，切换至工作目录，运行gclient
```
1 e:
2 cd e:\ws
3 gclient
```
等待安装python和git，大约五六分钟。

9.继续执行
```
python automate-git.py
``
根据提示选择合适配置参数

--download-dir 源码下载目录

--depot-tools-dir 工具包目录

--branch 源码分支(默认trunk主分支

--checkout 指定patch版本（默认最近提交 如何确定checkout下文介绍

--no-build 下载完不自动开始编译（我们需要修改编译配置

--no-update 确定源码下载完毕仅重新编译时使用

--force-build 强制编译（发现在有成功编译的时候再编译不会执行，可以加上这个

--no-debug-build 只编译release版本

--no-release-build 只编译debug版本
```
python automate-git.py --download-dir=e:\ws\source --depot-tools-dir=e:\ws\depot_tools --branch=2357 --checkout=d66017718b0f0d44da42b706c3c2aa5c0c103852 --no-build 
```
10.等待下载完成。睡上一觉，或盯着屏幕六小时。

五、如何确定--checkout值

如果确定需要分支下的最新版本，请略过此节。

1.从http://cefbuilds.com/检查目标版本的hash值。例：3.2357.1281.gd660177中的gd660177

2.从https://bitbucket.org/chromiumembedded/cef/branches/进入目标分支。例：2357

3.在Commits中检索目标hash，注意版本号中hash为8位，commits中为7位，可以忽略第一位搜索。例：gd660177->d660177

4.点击相应commit，即可在地址栏中得到checkout值。例：d66017718b0f0d44da42b706c3c2aa5c0c103852

六、修改编译配置

1.打开e:\ws\source\chromium\src\cef\cef.gypi，variables节下添加如下即可支持mp3。

```
 ‘proprietary_codecs‘: 1,
 ‘ffmpeg_branding‘: ‘Chrome‘,
```
同时可参考http://blog.csdn.net/chromium_webkit/article/details/8575629加入更多格式支持。

2.打开相同目录下cef.gyp，根据以下内容替换（--为待替换，++为替换后，其实这是一个patch文件，不知道如何用只好手动替换一下 新版本可能已包含了此patch）
```
@@ -7,7 +7,7 @@
     ‘pkg-config‘: ‘pkg-config‘,
     ‘chromium_code‘: 1,
     ‘grit_out_dir‘: ‘<(SHARED_INTERMEDIATE_DIR)/cef‘,
-    ‘about_credits_file‘: ‘<(SHARED_INTERMEDIATE_DIR)/about_credits.html‘,
+    ‘cef_about_credits_file‘: ‘<(SHARED_INTERMEDIATE_DIR)/cef_about_credits.html‘,
     ‘framework_name‘: ‘Chromium Embedded Framework‘,
     ‘revision‘: ‘<!(python tools/revision.py)‘,
     ‘chrome_version‘: ‘<!(python ../build/util/version.py -f ../chrome/VERSION -t "@MAJOR@.@MINOR@.@BUILD@.@PATCH@")‘,
@@ -659,26 +659,26 @@
       ],
     },
     {
-      ‘target_name‘: ‘about_credits‘,
+      ‘target_name‘: ‘cef_about_credits‘,
       ‘type‘: ‘none‘,
       ‘actions‘: [
         {
           ‘variables‘: {
             ‘generator_path‘: ‘../tools/licenses.py‘,
           },
-          ‘action_name‘: ‘generate_about_credits‘,
+          ‘action_name‘: ‘cef_generate_about_credits‘,
           ‘inputs‘: [
             # TODO(phajdan.jr): make licenses.py print inputs too.
             ‘<(generator_path)‘,
           ],
           ‘outputs‘: [
-            ‘<(about_credits_file)‘,
+            ‘<(cef_about_credits_file)‘,
           ],
           ‘hard_dependency‘: 1,
           ‘action‘: [‘python‘,
                      ‘<(generator_path)‘,
                      ‘credits‘,
-                     ‘<(about_credits_file)‘,
+                     ‘<(cef_about_credits_file)‘,
           ],
           ‘message‘: ‘Generating about:credits.‘,
         },
@@ -689,7 +689,7 @@
       ‘target_name‘: ‘cef_resources‘,
       ‘type‘: ‘none‘,
       ‘dependencies‘: [
-        ‘about_credits‘,
+        ‘cef_about_credits‘,
       ],
       ‘actions‘: [
         {
@@ -697,7 +697,7 @@
           ‘variables‘: {
             ‘grit_grd_file‘: ‘libcef/resources/cef_resources.grd‘,
             ‘grit_additional_defines‘: [
-              ‘-E‘, ‘about_credits_file=<(about_credits_file)‘,
+              ‘-E‘, ‘about_credits_file=<(cef_about_credits_file)‘,
             ],
           },
           ‘includes‘: [ ‘../build/grit_action.gypi‘ ],
```
等待两三个小时即可编译完成，偶然出错退出则加入--no-update重新执行，会继续上次的生成。
```
	http://images2015.cnblogs.com/blog/762652/201610/762652-20161006223128067-1198559515.png
	http://images2015.cnblogs.com/blog/762652/201610/762652-20161006223553489-403549574.png
```

```
include gallery images=images caption="编译完成测试结果mp3/mp4的支持 "cols=2
```      
      
See a [html5test](#) 测试结果mp3/mp4的支持 

八、参考资料

>https://bitbucket.org/chromiumfx/chromiumfx/overview

>https://bitbucket.org/chromiumembedded/cef/wiki/BranchesAndBuilding.md#markdown-header-automated-method

>https://github.com/cefsharp/cef-binary/wiki/Building-Cef-from-source

>http://cefbuilds.com/

>http://blog.csdn.net/chromium_webkit/article/details/8575629
>http://blog.csdn.net/chromium_webkit/article/details/8575629


