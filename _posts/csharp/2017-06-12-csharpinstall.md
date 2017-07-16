---
layout: blog
title:  "c#写一个自动安装包带7z自动解压释放安装"
background-image: http://ospsx22m9.bkt.clouddn.com/install.gif
date:   2017-05-13
category: csharp
tags:
- c#
- 打包安装
- debug打包
- winfrom
- c++
- zip
- 7z
- 自释放
- 自定义安装包
---
  
 
### 设置安装包参数和信息

```
        public string strCompanyNameEnglish = "liberxue";
        public string strProductNameEnglish = "liberxue";
        public string strServiceNameEnglish = "liberxue";
        public string strCompanyURL = "http://www.liberxue.com";
        private System.Collections.Specialized.StringCollection folderCol;
        private static ZipClass zipClass = new ZipClass();  //压缩工具类
        private static UnZipClass unZipC = new UnZipClass();  //解压缩类
```
* 注释下：解压和压缩类都是采用我的我GitHub公开的无dll类 毕竟谁也下载一个软件带一个dll解压类看起来多难受。。。怪怪得哈。
先上个图：
![install](http://ospsx22m9.bkt.clouddn.com/install.gif)
### 解压释放
```
 lblDoing.Text = "正在释放运行组建 . . .";
                lblLoadingDoing.Size = new Size(lblLoadingDoing.Size.Width + 35, lblLoadingDoing.Size.Height);
                Application.DoEvents();
                File.WriteAllBytes(strInstallationPath + "\\Debug.zip", Properties.Resources.Debug);

                string zipFile = txtPath.Text+ "\\Debug.zip";
                string unZipFileTo = txtPath.Text;   //解压到哪个目录下
                if (unZipFileTo == "")
                {
                    //showMsg("选择解压目标目录", "");
                   // new FrmMsg("安装中断", "选择解压目标目录", false).ShowDialog();
                    return;
                }
                ////判断文件是否是Zip文件
                //以及文件是否被修改。。。
                Application.DoEvents();
                if (isZipFile(zipFile))
                {
                    if (unZipC.UnZip(zipFile, unZipFileTo, "123"))
                    {
                        Application.DoEvents();
                        // PaintListView(unZipFileTo + "\\");
                        //  showMsg("解压成功", "");
                        // new FrmMsg("安装中断", "解压成功", false).ShowDialog();
                        lblDoing.Text = "系统组件释放成功";
                        lblLoadingDoing.Size = new Size(lblLoadingDoing.Size.Width + 55, lblLoadingDoing.Size.Height);
                        Application.DoEvents();
                        folderCol.Add(unZipFileTo + "\\");
                        Application.DoEvents();
                    }
                    else
                    {
                        new FrmMsg("安装中断", "错误:401\n杀毒软件拦截啦", false).ShowDialog();
                    }
                }
                else
                {
                    new FrmMsg("安装中断", "错误:403\n安装包被破坏或不完整", false).ShowDialog();
                }
```
### 写入安装信息和注册列表信息

```
                lblDoing.Text = "正在向系统注册程序...";
                lblLoadingDoing.Size = new Size(lblLoadingDoing.Size.Width + 85, lblLoadingDoing.Size.Height);
                Application.DoEvents();
                RegistryKey hm = Registry.LocalMachine;
                hm = Registry.LocalMachine.OpenSubKey("SOFTWARE\\Microsoft\\windows\\CurrentVersion\\Uninstall", true);
                RegistryKey psd = hm.CreateSubKey(strProductNameEnglish);
                psd.SetValue("DisplayIcon", strInstallationPath + "\\Edujoy_clientH5.exe");
                psd.SetValue("DisplayName", Application.ProductName);
                psd.SetValue("DisplayVersion", Application.ProductVersion);
                psd.SetValue("Publisher", Application.CompanyName);
                psd.SetValue("UninstallString", strInstallationPath + "\\UnInstall.exe");
                psd.SetValue("HelpLink", strCompanyURL);
                psd.SetValue("HelpTelephone", "4008009800");
                psd.SetValue("EstimatedSize", "45MB");
                psd.SetValue("URLInfoAbout", strCompanyURL);
```
### 快捷方式和开始菜单栏写入
```
  if (bStartMenuLink)
                    {
                        lblDoing.Text = "正在创建开始菜单快捷方式 ...";
                        lblLoadingDoing.Size = new Size(lblLoadingDoing.Size.Width + 92, lblLoadingDoing.Size.Height);
                        Application.DoEvents();
                        if (!Directory.Exists(Environment.GetFolderPath(Environment.SpecialFolder.StartMenu) + "\\" + Application.ProductName + "\\"))
                        {
                            Directory.CreateDirectory(Environment.GetFolderPath(Environment.SpecialFolder.StartMenu) + "\\" + Application.ProductName + "\\");
                        }
                        sc.Save(Environment.GetFolderPath(Environment.SpecialFolder.StartMenu) + "\\" + Application.ProductName + "\\" + Application.ProductName + ".lnk");
                        sc = new Installation.Common.Shortcut();
                        sc.Path = strInstallationPath + "\\EdujoyLogin.exe";
                        sc.Arguments = "";
                        sc.WorkingDirectory = strInstallationPath;
                        sc.Description = "";
                        sc.Save(Environment.GetFolderPath(Environment.SpecialFolder.StartMenu) + "\\" + Application.ProductName + "\\卸载.lnk");
                    }
                }
```
### 安装完成善后工作
```
 lblDoing.Text = "正在清理安装时释放的临时文件...";
                lblLoadingDoing.Size = new Size(lblLoadingDoing.Size.Width + 95, lblLoadingDoing.Size.Height);
                Application.DoEvents();
            REDELETE:
                try
                {
                    File.Delete(strInstallationPath + "\\Debug.zip");
                   // File.Delete("liberxue.InstallationLog");
                }
                catch 
                {
                    Thread.Sleep(10);
                    Application.DoEvents();
                    goto REDELETE;
                }
```
* 很头疼的是一个 错误“winform 创建新注册表时出现“不允许所请求的注册表访问权”，解决办法”备忘下：

>>>关于写入注册列表和控制面板卸载的注册列表注释下
DisplayName 名称
DisplayVersion 显示版本
InstallDate 安装日期
InstallSource 安装源
Language 语言
ModifyPath 修改命令
NoModify 禁止修改
NoRepair 禁止修复
Publisher 发布者
UninstallString 卸载命令
Version 版本
VersionMajor 主要版本
VersionMinor 次要版本
HelpLink 帮助链接
WindowsInstaller 通过Windows Install安装
SystemComponent 系统组件
EstimatedSize 大小
LogFile 日志文件
LogMode 记录模式
URLInfoAbout 关于链接
Comments 注释
HelpTelephone 技术支持电话
InstallLocation 安装目标地址
Readme Readme文档所在地
URLUpdateInfo 升级信息链接
NoRemove 禁止删除(耍贱...)
Contact 联系方式
DisplayIcon 图标
QuietUninstallString 安静卸载模式命令

- 这样就搞定了 很简单有没有哈哈 该代码我会开源在GitHub上面请关注，下一篇我介绍下c#自定义卸载





