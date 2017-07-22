---
layout: blog
title:  "两分钟vscode在Mac安装Python环境"
date:   2017-07-22 16:04:49
category: Python
background-image: http://ot1cc1u9t.bkt.clouddn.com/17-7-22/62395302.jpg
tags: 
- vscode
- Python
- Mac
---
周末实在无聊学学Python 大概看了下人们手册 貌似不难 语法很简单哈
 
开始下载一个``pycharm``晕死。。。8G内存 风扇呼呼呼。。。。
 
``JetBrains``是出精品啊，但是这性能。。。
 
此路不通啊
 

## 环境：
```
Mac 10.12.6 Beta (16G24b)
vscode：1.14.1
```

# Mac 默认自带Python 
## 自带Python环境
```
Python2.7

```
所以呢压根不需要安装 
## iTerm测试下Python

```
print "hello,python!"

```
## control+D退出

# 打开vscode

新建一个hello.py 文件名任意只要是.py就可以
````
print "hellow,world!"
````
control +s 保存 
vscode会自动提醒安装Python环境
fn+5
vscode提醒需要``pip install pylint``
pylint是Python代码检查工具让你的Python更规范
好吧那就安装 当点击install时间。。。。
 
# 安装pip install pylint

## 默认情况下报错如下：
 
```
Exception:
Traceback (most recent call last):
  File "/Library/Python/2.7/site-packages/pip-9.0.1-py2.7.egg/pip/basecommand.py", line 215, in main
    status = self.run(options, args)
  File "/Library/Python/2.7/site-packages/pip-9.0.1-py2.7.egg/pip/commands/install.py", line 342, in run
    prefix=options.prefix_path,
  File "/Library/Python/2.7/site-packages/pip-9.0.1-py2.7.egg/pip/req/req_set.py", line 784, in install
    **kwargs
  File "/Library/Python/2.7/site-packages/pip-9.0.1-py2.7.egg/pip/req/req_install.py", line 851, in install
    self.move_wheel_files(self.source_dir, root=root, prefix=prefix)
  File "/Library/Python/2.7/site-packages/pip-9.0.1-py2.7.egg/pip/req/req_install.py", line 1064, in move_wheel_files
    isolated=self.isolated,
  File "/Library/Python/2.7/site-packages/pip-9.0.1-py2.7.egg/pip/wheel.py", line 345, in move_wheel_files
    clobber(source, lib_dir, True)
  File "/Library/Python/2.7/site-packages/pip-9.0.1-py2.7.egg/pip/wheel.py", line 316, in clobber
    ensure_dir(destdir)
  File "/Library/Python/2.7/site-packages/pip-9.0.1-py2.7.egg/pip/utils/__init__.py", line 83, in ensure_dir
    os.makedirs(path)
  File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/os.py", line 157, in makedirs
    mkdir(name, mode)
OSError: [Errno 13] Permission denied: '/Library/Python/2.7/site-packages/enum'
```
## 不用管权限问题直接在vscode终端

```
sudo /usr/bin/python -m pip install pylint
``` 
ok 
## 开始Python世界吧。。。

```
import time
import webbrowser
while time.time() < 1500642585:
    time.sleep(1)
webbrowser.open('https://www.google.com')
```


> 群里一大波人开始嘚瑟写代码就应该用Vim，嗯好吧 写c#和c++  Java 我怎么没看到有人用Vim。。。以后少用QQ尽量用gitter.im
