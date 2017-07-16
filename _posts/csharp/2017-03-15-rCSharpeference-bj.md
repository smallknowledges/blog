---
layout: blog
title:  "C#将DLL嵌入整合reference到成为独立exe打包"
date:   2017-03-15
background-image: http://ot1cc1u9t.bkt.clouddn.com/17-7-16/62316631.jpg
category: csharp
tags:
- 打包
- dll
- c#
- reference
---
 
>动态加载程序集时有时引用的程序集会有依赖项，就会报各种异常；
在网上搜索了很久,终于找到了解决方法,不废话如下.
先把DLL放进资源库里,然后在dll属性里面的BuildAction选择Embedded Resource.
接着在References里面直接引用你要的dll.
接下来把下面的代码放在你要执行的类里面:

```
System.Reflection.Assembly CurrentDomain_AssemblyResolve(object sender, ResolveEventArgs args)  
       {  
           string dllName = args.Name.Contains(",") ? args.Name.Substring(0, args.Name.IndexOf(‘,‘)) : args.Name.Replace(".dll", "");  
   
           dllName = dllName.Replace(".", "_");  
   
           if (dllName.EndsWith("_resources")) return null;  
   
           System.Resources.ResourceManager rm = new System.Resources.ResourceManager(GetType().Namespace + ".Properties.Resources", System.Reflection.Assembly.GetExecutingAssembly());  
   
           byte[] bytes = (byte[])rm.GetObject(dllName);  
   
           return System.Reflection.Assembly.Load(bytes);  
       }  
　　　　public Form1()//看清楚这是窗体本来的初始化函数
       {
            //在InitializeComponent()之前调用

    　　　　 AppDomain.CurrentDomain.AssemblyResolve += new ResolveEventHandler(CurrentDomain_AssemblyResolve);
            InitializeComponent();
       }
       }
```