---
layout: blog
title: "c#创建可动态修改src的unity3d插件UnityWebPlayer"
background: red
date:  2016-06-16
category: csharp
background-image: http://ot1cc1u9t.bkt.clouddn.com/17-7-16/30065910.jpg
tags:
- unity3d
- UnityWebPlayer
- U3D
- c#
- winfrom
---
 
UnityWebPlayer是unity3d的一个播放器插件，在C# winform中使用它并对src属性赋值即可调用对应的U3D场景，实现三维立体展示。

但此src属性只接受绝对路径，并且不能在程序运行时动态修改，此特性对程序可移植性造成了很大影响。但如果动态创建控件并赋值即可消除影响。

首先在工具箱中右键“选择项”，在弹出的对话框中跳转至“COM组件”，选择UnityWebPlayer添加进工具箱即可（如果没有此项可点此<a href="http://webplayer.unity3d.com/setup-3.x" target="_blank">安装</a>）

- com组件导入

 
- 动态创建unitywebplayer控件的函数代码如下：
 
```
private void CreateUnityWebPlayer()
{
    var unity = new AxUnityWebPlayerAXLib.AxUnityWebPlayer();
    ((System.ComponentModel.ISupportInitialize)(unity)).BeginInit();
    Controls.Add(unity);
    ((System.ComponentModel.ISupportInitialize)(unity)).EndInit();
    unity.src = "此处请填写.unity3d源文件的绝对路径";
    AxHost.State state = unity.OcxState;
    unity.Dispose();
    unity = new AxUnityWebPlayerAXLib.AxUnityWebPlayer();
    ((System.ComponentModel.ISupportInitialize)(unity)).BeginInit();
    this.SuspendLayout();
    unity.Name = "Unity";
    unity.OcxState = state;
    unity.Dock = DockStyle.Fill;
    this.Controls.Add(unity); 
    ((System.ComponentModel.ISupportInitialize)(unity)).EndInit();
    this.ResumeLayout(false);
}
```

