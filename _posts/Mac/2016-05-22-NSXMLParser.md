---
layout: blog
title: "parser:parseErrorOccurred31#parser:parseErrorOccur"
category: Mac
background-image: http://ot1cc1u9t.bkt.clouddn.com/17-7-16/11722802.jpg
date:  2016-05-22
tags: 
- swift
- ios
- NSXMLParser
- 错误编码是31
---
 
```
/取XML文件的前40个字节
        NSData * xmldata = [self.ItemData subdataWithRange:NSMakeRange(0,40)];
 
        //以UTF-8编码进行解码
        NSString *xmlstr = [[NSString alloc] initWithData:xmldata encoding:NSUTF8StringEncoding];
        //NSLog(@"XML HEADER: %@", xmlstr);
        //搜索GB2312，如果找到，就对整个文件进行编码转换        
        if ([xmlstr rangeOfString:@"\"GB2312\"" options:NSCaseInsensitiveSearch].location != NSNotFound)
        {
         //   NSLog(@"GB2312 encoding founded.");
            
            NSStringEncoding enc = CFStringConvertEncodingToNSStringEncoding(kCFStringEncodingGB_18030_2000);
            NSString *utf8str = [[[NSString alloc] initWithData:self.ItemData encoding:enc] autorelease];
            utf8str = [utf8str stringByReplacingOccurrencesOfString:@"\"GB2312\"" withString:@"\"utf-8\"" options:NSCaseInsensitiveSearch range:NSMakeRange(0,40)];
            NSData *newData = [utf8str dataUsingEncoding:NSUTF8StringEncoding];
            self.ItemData = newData;   
        }
}
```
***
GB2312或GBK编码的网页，要先用gb编码解码，然后替换其中的gb字符串，再用utf8编码成data给parser就能解析中文网页了。

NSXMLParser 将停止解析在遇到特殊字符后

我读一个 XML 文件从谷歌天气 api 和解析它使用 NSXMLParser。城市问题是巴黎。这是我得到的简短 xml 输出
```
<?xml version="1.0"?>
    <xml_api_reply version="1">
    <weather module_id="0" tab_id="0" mobile_row="0" mobile_zipped="1" row="0" section="0" ><forecast_information>
    <city data="Paris, Île-de-France"/>
    <postal_code data="Paris"/>
    <latitude_e6 data=""/>
    <longitude_e6 data=""/> 
...
...
}
```

现在我用来削去此 xml 的代码是

```
NSString *address = @"http://www.google.com/ig/api?weather=Paris";
    NSURL *URL = [NSURL URLWithString:address];

NSXMLParser *parser = [[NSXMLParser alloc] initWithContentsOfURL:URL];
    [parser setDelegate:self];
    [parser parse];
...

- (void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qualifiedName attributes:(NSDictionary *)attributeDict 
{

    NSLog(@"XML Parser 1 ... elementName ... %@", elementName);

}
```

这是我获得上述 xml 的输出
***

```
XML Parser 1 ... elementName ... xml_api_reply
XML Parser 1 ... elementName ... weather
XML Parser 1 ... elementName ... forecast_information
```
问题是它分析的所有标记，直到它到达"城市数据"因为巴黎 î l e de 法国，然后它就会停止的名称中有非 ascii 字符。它不会处理之后像 postal_code 的标签。纬度、 经度等。
所以我的问题是，有什么办法可以从返回的 URL XML 字符串中删除所有非 ascii 字符吗？
解决方法 1:

还行。我已经解决了此问题。这是怎么弄来的工作。
我首先做的就是用特殊字符的 URL 的 XML。然后我去掉从 XML 字符串的所有特殊字符。然后我将字符串转换为 NSdata 然后把 nsdata 对象传递给我的 NSXMLParser。因为它有没有更多特殊字符 NSXMLParser 是快乐。
这里是为任何人在将来可能会遇到的代码。大感谢您对这篇文章作出了贡献的人 ！

```
SString *address = @"http://www.google.com/ig/api?weather=Paris";
    NSURL *URL = [NSURL URLWithString:address];
    NSError *error;    
    NSString *XML = [NSString stringWithContentsOfURL:URL encoding:NSASCIIStringEncoding error:&error];

    //REMOVE ALL NON-ASCII CHARACTERS
         NSMutableString *asciiCharacters = [NSMutableString string];
         for (NSInteger i = 32; i < 127; i++)  
         {
         [asciiCharacters appendFormat:@"%c", i];
         }

         NSCharacterSet *nonAsciiCharacterSet = [[NSCharacterSet characterSetWithCharactersInString:asciiCharacters] invertedSet];

         XML = [[XML componentsSeparatedByCharactersInSet:nonAsciiCharacterSet] componentsJoinedByString:@""];

    NSData *data = [XML dataUsingEncoding:NSUTF8StringEncoding];
    NSXMLParser *parser = [[NSXMLParser alloc] initWithData:data];
    [parser setDelegate:self];
    [parser parse];
```
