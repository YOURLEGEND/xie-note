**目录**

[Apktool的准备](#t0)

[Apktool解压APP](#t1)

[Apktool打包APP](#t2)

[对APP进行签名](#t3)

[dex2jar反编译APP](#t4)

[JD-GUI的使用](#t5)

[baksmali反编译.dex文件](#t6)

[smali编译成.dex文件](#t7)

* * *

对APP进行[渗透测试](https://so.csdn.net/so/search?q=%E6%B8%97%E9%80%8F%E6%B5%8B%E8%AF%95&spm=1001.2101.3001.7020)，那么就需要对APP本身的客户端安全做一个检测。所以就需要对APP进行解压、反编译等操作。APP文件的后缀名为.apk。APP反编译软件有个组合套餐：apktool 、dex2jar 和 jd-gui

*   apktool：可以解压软件的布局文件、图片等资源，方便大家学习一些很好的布局；
*   dex2jar：将APK反编译成java源码（classes.dex转化成jar文件）；
*   jd-gui：查看APK中classes.dex转化成出的jar文件，即源码文件。

### Apktool的准备

1：Apktool的下载页面：[https://bitbucket.org/iBotPeaches/apktool/downloads/](https://bitbucket.org/iBotPeaches/apktool/downloads/)  ，随便下载一个，我这里下载的是最新版本的 apktool\_2.4.0.jar 。

2：将下载好的 apktool\_2.4.0.jar 重命名为 apktool.jar

3：新建文本文件，将下面的脚本复制到文本并保存，然后重命名为apktool.bat;

```
@echo off      
if "%PATH_BASE%" == "" set PATH_BASE=%PATH%      
set PATH=%CD%;%PATH_BASE%;      
chcp 65001 2>nul >nul      
java -jar -Duser.language=en -Dfile.encoding=UTF8 "%~dp0\apktool.jar" %*
```


### Apktool解压APP

将 apktool.bat 和 apktool.jar 文件放到同一文件夹下，在该文件夹下打开cmd窗口，执行以下命令

```
apktool.bat -r d -f  test.apk
```


![](https://img-blog.csdnimg.cn/20190914204032392.png)

然后会在该文件夹下生成同 apk 名的文件夹

![](https://img-blog.csdnimg.cn/20190909154608652.png)

文件夹内容如下，可以得到 xml、smali反汇编代码(有关于smali详细：[逆向之Smali入门学习](https://www.jianshu.com/p/40908a016480))、res资源文件、assets配置文件、lib库文件，我们可以直接搜索smali文件和资源文件来查找链接等。

![](https://img-blog.csdnimg.cn/20190914204134966.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### Apktool打包APP

```
# apktool.bat b -f 要打包的文件夹路径 -o 输出的apk路径      
apktool.bat b -f C:\Users\administrator\desktop\apktool\test -o C:\Users\administrator\desktop\apktool\test2.apk
```


### ![](https://img-blog.csdnimg.cn/20190914204717832.png)

![](https://img-blog.csdnimg.cn/20190914204744316.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 对APP进行签名

将app打包完成之后，还不能完。在Android中，包名相同的两个APK会被认为是同一个应用。当新版本覆盖旧版本时，签名证书必须一致，否则会被拒绝。（即使开启了“允许未知来源的应用”）。如果APK没有使用自己的证书进行签名，将会失去对版本管理的主动权。

```
java -jar signapk.jar testkey.x509.pem testkey.pk8 C:\Users\administrator\desktop\apktool\test2.apk C:\Users\administrator\desktop\apktool\test3.apk
```


### ![](https://img-blog.csdnimg.cn/20190915093924161.png)

所以最后的test3.apk就是我们打包后签名后的APK。

![](https://img-blog.csdnimg.cn/20190915093946752.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### dex2jar反编译APP

首先修改 apk 为 zip 扩展名，然后解压出 classes.dex 文件。.dex 文件是 dalvik 虚拟机可识别的可执行文件，是java层的主要代码，即主程序。详细：[https://www.jianshu.com/p/40908a016480](https://www.jianshu.com/p/40908a016480)

**APP包结构：**

*   classes.dex：java层的主要代码，即主程序
*   META-INF：签名和证书相关文件 ·
*   lib：native层的动态链接库文件（.so）目录，按照CPU架构作为子目录存放不同的so
*   assets：打包的静态文件，通常存放需要读取的图片、加密代码等等
*   res：资源目录
*   AndroidManifest.xml：配置文件，默认状态为编译后的二进制文件
*   resources.arsc： 资源配置文件，默认状态为编译后的二进制文件

![](https://img-blog.csdnimg.cn/20190909141629492.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

将classes.dex文件复制到dex2jar文件夹内

![](https://img-blog.csdnimg.cn/20190909160158544.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在该文件夹下打开cmd窗口，执行以下命令，得到 classes-dex2jar.jar 文件

```
d2j-dex2jar.bat classes.dex
```


![](https://img-blog.csdnimg.cn/20190909160502154.png)

![](https://img-blog.csdnimg.cn/20190914205017319.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### JD-GUI的使用

用 jd-[gui](https://so.csdn.net/so/search?q=gui&spm=1001.2101.3001.7020) 打开 classes-dex2jar.jar 文件。

![](https://img-blog.csdnimg.cn/20190909160710434.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### baksmali反编译.dex文件

直接运行命令，就在运行目录下创建一个 out 文件夹，生成所有对应的smali文件。

```
java -jar baksmali-2.3.4.jar d classes.dex
```


![](https://img-blog.csdnimg.cn/20191027234632609.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### smali编译成.dex文件

如果要将 out 文件夹编译成 .dex 文件，将会生成 out.dex

```
java -jar smali-2.3.4.jar a out
```


![](https://img-blog.csdnimg.cn/20191027235026468.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

参考文章：[http://blog.sina.com.cn/s/blog\_7c6cbaf601019604.html](http://blog.sina.com.cn/s/blog_7c6cbaf601019604.html)

相关文章：[移动APP安全在渗透测试中的应用](https://www.freebuf.com/articles/web/29421.html)

                  [移动APP安全测试](https://blog.51cto.com/laoyinga/2155341)

                  [五大APP安全在线检测平台对比](http://www.voidcn.com/article/p-hxfwmzou-bqt.html)

                 [移动APP渗透测试方案 展示5个方面总结3种常见漏洞—转载绿盟科技](https://blog.csdn.net/xl_lx/article/details/78399800)