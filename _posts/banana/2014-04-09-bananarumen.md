---
layout: blog
banana: true
category: banana
title: BANANA PI 入门：香蕉派如何安装系统？
background-image: http://ot1cc1u9t.bkt.clouddn.com/17-7-16/21437938.jpg
tags:  
- BANANA
- banana
- Api
- no-cache
- 香蕉派
---

 
香蕉派的系统是安装在SD卡中的，想要把系统安装到SD卡中，你需要一台运行Windows或Linux的PC，香蕉派能运行的系统很多，但是现在大部分还在测试阶段，官方放出来的有两三个系统。社区内的一些高手也放出来了一些，相信在不久的将来，我能能选择的越来越多。

这篇文章只适合，我这样的小白用户来阅读，大婶们都可以绕道了。

目前香蕉派能安装的系统：

#### 一、Cubieboard_For_BananaPi_v1.0.tgz

官方放出的系统，根据Cubieboard制作。系统前景不明。。。

#### 二、RaspberryPi_For_BananaPi_v1.0.tgz

官方放出的系统，根据raspbian制作。个人推荐使用这个系统，资料比较多，出现问题好解决。

#### 三、Android4.2_For_BananaPi_v1.0

大婶放出来的系统，可以尝鲜，不太推荐，运行速度还需要优化，默认集成的是谷歌应用商店，速度比较慢，用浏览器下载应用识别是IOS，不知怎么回事。用浏览器下载应用不成功，可以尝试下载好放到U盘里，复制进去。很期待作者能对这个系统做进一步的优化。

#### 四、精简版的Ubuntu14.4

这个也是大婶发出来的，需要有一定的基础才能折腾，官方正在催img包。希望时间不会太长。

一和二都是卡刷包，可一直接用SD卡烧录工具进行烧录，然后插上香蕉派就能直接启动了，很方便。

香蕉派安装Cubieboard_For_BananaPi_v1.0、RaspberryPi_For_BananaPi_v1.0系统教程

##### 下载系统镜像文件

系统镜像文件下载地址：http://www.lemaker.org/index.php?m=content&c=index&a=show&catid=29&id=4

## Windows环境：

##### 格式化SD卡

1、下载SD卡格式化工具，例如 SD Formatter ：https://www.sdcard.org/downloads/formatter_4/eula_windows/

2、解压下载的文件，并运行setup.exe。

3、在 “选项设置” 选项里, 设置 “格式化类型” 选项为 快速格式化, “逻辑大小调整” 选项为 “开启(ON)”。

4、确认插入的SD卡盘符和选择的盘符一致。

5、点击 “格式化（Format）” 按钮。

##### 写入镜像文件到SD卡

1、解压下载的系统镜像文件，你会发现解压后的文件有将近8个G的大小，不要惊慌，文件本身是没这么大的，这样做是为了使用8GSD卡的全部空间，这样安装系统后就不需要再做别的操作了。

2、下载镜像写入工具，例如 Win32 Diskimager ：http://sourceforge.net/projects/win32diskimager/files/Archive/

3、打开已经解压的镜像文件，选择好系统镜像文件的路径和SD卡的盘符，然后点击write，等待一会SD卡就算烧录好了。

## linux

###### 格式化SD卡

1、运行 ``fdisk –l ``命令确认SD卡的盘符。

2、运行 ``sudo fdisk /dev/sdx ``命令. 使用 d 命令去删除SD卡的所有分区，然后使用n 命令去添加一个新的分区，最后使用 w 命令保存退出。

3、运行 ``sudo mkfs.vfat /dev/sdxx ``命令去格式化SD卡的所有分区为FAT32格式。 (根据你的SD卡盘符来替换x)，你也可以跳过这一步，因为Linu下的dd 命令会自动格式化SD卡。

###### 写入镜像文件到SD卡

1、运行 ``unzip [path]/[downloaded filename] ``命令解压。如果文件后缀名是 .tgz, 则运行``tar zvxf [path]/[downloaded filename] ``命令解压。
确保文件名和文件的完整路径没有空格和汉字。

2、运行 ``sudo fdisk –l`` 命令确认SD卡的盘符。

3、确认镜像文件的hash key和下载页面提供的一致。（可选）

``sha1sum [path]/[imagename]``

这将会输出一长串数字，应该和你下载的镜像页面的”SHA-1″ 那一行匹配。

4、运行 ``umount /dev/sdxx ``命令卸载SD卡的所有分区。

5、运行 sudo dd bs=4M if=[path]/[imagename] of=/dev/sdx 命令去写入镜像文件。耐心等待镜像写入。如果块大小4M 不能运行，请换用1M，尽管这回花费更长时间。你可以使用 sudo pkill –USR1 –n –x dd 命令去查看进度。

香蕉派安装Android4.2_For_BananaPi_v1.0系统教程

##### 下载镜像文件

镜像下载地址：http://pan.baidu.com/s/1c0cP2ms

#####  写入镜像到到SD卡

#### SD卡格式化的方法可以参考上面的方法。

- 下载SD卡烧录工具

SD卡烧录，需要用到 PhoenixCard http://pan.baidu.com/s/1xStaA

由于本Android镜像不适用于Linux下的dd命令和Windows下的Win32Diskimager软件进行烧录，所以需要使用PhoenixCard这款软件进行SD卡制作。（注：如出现使用笔记本自带SD卡槽无法正常烧录，请换用USB读卡器。）

- 恢复卡

插入SD卡后选择正确的盘符，选择已经下载的.img系统镜像文件，选择“卡启动”，然后点击“恢复卡”。

恢复成功后，点击“确定”。

- 烧录卡

SD卡恢复成功，点击“烧录”。这是一个很漫长的过程，请耐心等待。

香蕉派安装最小Ubuntu14.04系统教程

1. 下载image文件

```
u-boot, script.bin, kernel: http://pan.baidu.com/s/1i3sorhr
ubuntu:（含13.04和14.04根据需要自己选择） http://pan.baidu.com/s/1c0paF88
```

2. 烧写u-boot

```
$ sudo dd if=/dev/zero of=/dev/sdb bs=1M count=1
$ sudo dd if=u-boot-sunxi-with-spl.bin of=/dev/sdb bs=1024 seek=8
$ sudo dd if=/dev/zero of=/dev/sdb bs=1M count=1
$ sudo dd if=u-boot-sunxi-with-spl.bin of=/dev/sdb bs=1024 seek=8
```
3. SD 卡分区

```
$ sudo fdisk /dev/sdb
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x67abb3e5.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): 
Using default response p
Partition number (1-4, default 1): 
Using default value 1
First sector (2048-1990655, default 2048): 
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-1990655, default 1990655): +20M

Command (m for help): n
Partition type:
   p   primary (1 primary, 0 extended, 3 free)
   e   extended
Select (default p): 
Using default response p
Partition number (1-4, default 2):
Using default value 2
First sector (43008-1990655, default 43008): 
Using default value 43008
Last sector, +sectors or +size{K,M,G} (43008-1990655, default 1906595): 
Using default value 1990655

Command (m for help):w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```
```
$ sudo fdisk /dev/sdb
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x67abb3e5.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.
 
Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)
 
Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): 
Using default response p
Partition number (1-4, default 1): 
Using default value 1
First sector (2048-1990655, default 2048): 
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-1990655, default 1990655): +20M
 
Command (m for help): n
Partition type:
   p   primary (1 primary, 0 extended, 3 free)
   e   extended
Select (default p): 
Using default response p
Partition number (1-4, default 2):
Using default value 2
First sector (43008-1990655, default 43008): 
Using default value 43008
Last sector, +sectors or +size{K,M,G} (43008-1990655, default 1906595): 
Using default value 1990655
 
Command (m for help):w
The partition table has been altered!
 
Calling ioctl() to re-read partition table.
Syncing disks.

```
- 分区创建好了，下面进行格式化。

```
$ sudo mkfs.vfat /dev/sdb1 $ sudo mkfs.ext2 /dev/sdb2

$ sudo mkfs.vfat /dev/sdb1 $ sudo mkfs.ext2 /dev/sdb2
```
-  复制script.bin和uImage文件

```
$ sudo mount /dev/sdb1 /mnt
$ sudo cp script.bin /mnt
$ sudo cp uImage /mnt/
$ sudo umount /mnt
```
```
$ sudo mount /dev/sdb1 /mnt
$ sudo cp script.bin /mnt
$ sudo cp uImage /mnt/
$ sudo umount /mnt
```
#####  解压ubuntu文件系统

```
$ sudo mount /dev/sdb2 /mnt
$ sudo tar -xvf ubuntu-13.04.tgz -C /mnt
$ sudo umount /mnt
```
```
$ sudo mount /dev/sdb2 /mnt
$ sudo tar -xvf ubuntu-13.04.tgz -C /mnt
$ sudo umount /mnt
```

- 将SD卡插入BananaPi上面，上电，等待着ubuntu启动起来，然后，就可以随心所欲啦。

- 是不是不知道用户名和密码呢？猜一猜？是BananaPi呢？还是bananapi呢？

> 用户名：bananapi 密码：bananapi

### 安装SD启动香蕉派

SD卡烧录好之后，插到香蕉派上，再给香蕉派供上电，香蕉派就能启动了，想要操作树莓派还需要其他一些外设！

1、将写好镜像的SD卡插入香蕉派底面左侧的SD卡槽。

2、板子上面下边中间处是标准HDMI接口，在SATA接口右侧。用HDMI线连接你的香蕉派和HDMITV或者显示器。

3、如果没有HDMI或者DVI接口的显示器，你可以使用上边中间处黄色的AV接口和右侧的音频接口来输出视频和音频到模拟TV或显示器。 将USB键盘和鼠标插入右边的USB接口。

4、将USB键盘和鼠标插入右边的USB接口。

5、USB接口下面是网口，你可以把香蕉派接入有线网络。

6、底边左侧有一个micro-USB电源输入接口，连接一个5V和至少1A的电源适配器。比1A大也可以。避免使用较小功率的GSM电话充电器，及时上面标明了”5V 1A”，它也不一定能够输出1A。

7、注意不是下图左侧的mini-USB接口，虽然它和micro-USB接口很像。

8、如果你有一个不用的3.5英寸硬盘，你可以把他接到香蕉派上。这将会用到一个SATA连接接口。将SATA线一端接到micro-USB和HDMI中间的SATA口，另一端接到硬盘上。并且将电源线接到SATA POWER接口。注意不要接错线。（这一步可以跳过）

如果一切进展顺利，插电后屏幕将显示操作系统界面。
