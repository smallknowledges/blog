---
layout: blog
istop: true
title: "c#交互JavaScript开发建立捕获前端错误日志"
background-image: https://o243f9mnq.qnssl.com/2017/06/116099051.jpg
date:  2017-07-29 01:12:01
category: javascript
tags:
- 捕获前端错误日志
- JavaScript
- console
- oneerror
- c#
- winfrom
- handlerr
---

# 实现目的
 
之前开发winfrom和前端交互时间开发工程一直预留了debug console（chormeF12）功能，但是很多时间在项目上线和用户使用过程难免会出现部分错误和异常，想实现一个前端日志库，找了许久一直使用try...catch 但是效果不是很理想，作为强迫症患者onerror虽然实现的目的一样但是貌似跟c#错误日志类一样哈记录下来备用；
 
# onerror事件函数
如果需要利用 onerror 事件，就必须创建一个处理错误的函数。

## onerror事件处理器 
可以把这个函数叫作 onerror 事件处理器 (onerror event handler)。这个事件处理器使用

### 三个参数来调用：

- msg（错误消息）
- url（发生错误的页面的 url）
- line（发生错误的代码行）

# oneerror语法：

```JavaScript
onerror=handleErr

function handleErr(msg,url,l)
{
//错误输出在这哈
return true or false
}
```
## 原理

浏览器是否显示标准的错误消息，取决于``onerror``的返回值。如果返回值为``false``，则在控制台 (JavaScript console) 中显示错误消息。反之则不会。
 
## 来个栗子
 onerror 事件来捕获错误
  
```html
<html>
<head>
<script type="text/javascript">
onerror=handleErr
var txt=""

function handleErr(msg,url,l)
{
txt="本页中存在错误。\n\n"
txt+="错误：" + msg + "\n"
txt+="URL: " + url + "\n"
txt+="行：" + l + "\n\n"
txt+="点击“确定”继续。\n\n"
alert(txt)
return true
}

function message()
{
adddlert("Welcome guest!")
}
</script>
</head>

<body>
<input type="button" value="查看消息" onclick="message()" />
</body>
</html>
```
# JavaScript传错误给c#

```JavaScript
    <script>
    var $client;
     (function ($client) {
     function setRuntimeInfo(cefRuntimeInfo) {
         ....}
    $client.setRuntimeInfo = setRuntimeInfo;})

```
# c#接收JavaScript错误demo
```c#
        /// <summary>
        /// 接收成功
        /// </summary>
        /// <param name="contents">当前文档中的内容</param>
        /// <returns>如果接收成功则返回true</returns>
        internal bool jsapiTOsharp(string contents)
        {
            JObject getfileext = (JObject)JsonConvert.DeserializeObject(contents);
            string msg = getfileext["fileext"].ToString();
            string strname = getfileext["filename"].ToString();
            string strUrl = getfileext["filePath"].ToString();
            if (msg == "DOC" || msg == "DOCX" || msg == "PPTX" || msg == "PPT")
            { SaveFile(strUrl, strname); }
            else if (msg == "MP4" || msg== "OGG")
            {
                Openplayer(contents);
            }
            else {
                new FrmMsg("系统提示", "格式不支持,请联系管理员", false).ShowDialog();
                Toolslog.WriteLog("格式不支持" + "\r\n" + contents.ToString());
            }
            return false;
        }
```

## c#写入错误日志
```c#
 Toolslog.WriteLog("格式不支持" + "\r\n" + contents.ToString());
```
## c#自定义错误日志类
```c#
using System;
using System.IO;

namespace liberxue
{
    public class Toolslog
    {
        public static void WriteLog(string strLog)
        {
            string sFilePath = System.Environment.CurrentDirectory + "\\log";
            string sFileName = DateTime.Now.ToString("yyyy-MM-dd") + ".log";
            sFileName = sFilePath + "\\" + sFileName; //文件的绝对路径
            if (!Directory.Exists(sFilePath))//验证路径是否存在
            {
                Directory.CreateDirectory(sFilePath);
                //不存在则创建
            }
            FileStream fs;
            StreamWriter sw;
            try
            {
                if (!File.Exists(sFileName)) //验证文件是否存在，有则追加，无则创建
                {

                    fs = new FileStream(sFileName, FileMode.Append, FileAccess.Write);
                    sw = new StreamWriter(fs);
                    sw.WriteLine(DateTime.Now.ToString("---------------[" + "yyyy-MM-dd HH:mm:ss"+ "]") + "---------------" + strLog);
                    sw.Close();
                    fs.Close();
                    fs.Dispose();
                }
                else {

                    fs = new FileStream(sFileName, FileMode.Append, FileAccess.Write);
                    sw = new StreamWriter(fs);
                    sw.WriteLine(DateTime.Now.ToString("---------------[" + "yyyy-MM-dd HH:mm:ss" + "]") + "---------------" + strLog);
                    sw.Close();
                    fs.Close();
                    fs.Dispose();
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("写入日志错误{0}", ex.ToString());
            }
        }

        internal static void WriteLog(string v1, string v2)
        {
            throw new NotImplementedException();
        }
    }
}

```