**目录**

[John the Ripper](#t0 "John the Ripper")

[破解Linux系统密码](#t1 "破解Linux系统密码")

[破解Windows系统密码](#t2 "破解Windows系统密码")

* * *

John the Ripper
---------------

**John the Ripper**是一个快速的密码破解工具，用于在已知密文的情况下尝试破解出明文，支持目前大多数的[加密算法](https://so.csdn.net/so/search?q=%E5%8A%A0%E5%AF%86%E7%AE%97%E6%B3%95&spm=1001.2101.3001.7020)，如DES、MD4、MD5等。它支持多种不同类型的系统架构，包括Unix、Linux、Windows、DOS模式、BeOS和OpenVMS，主要目的是破解不够牢固的Unix/Linux系统密码。除了在各种Unix系统上最常见的几种密码哈希类型之外，它还支持Windows LM散列，以及社区增强版本中的许多其他哈希和密码。它是一款开源软件。Kali中自带John

*   可执行文件位置：    /usr/sbin/john
*   密码字典所在目录：/usr/share/john/

John the Ripper支持字典破解方式和暴力破解方式。

![](https://img-blog.csdnimg.cn/20200927212854367.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 破解Linux系统密码

破解Linux用户密码需要使用到两个文件（包含用户的信息和密码hash值）

*   /etc/passwd       包含用户信息的文件
*   /etc/shadow       包含密码信息的文件

然后我们创建一个 test 用户，密码设置为 password ，用来测试

![](https://img-blog.csdn.net/20181025094132487?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

使用 unshadow 命令组合 /etc/passwd 和 /etc/shadow ，组合成 test\_passwd 文件。其实 test\_passwd 就是 /etc/passwd 和 /etc/shadow 的简单组合:   unshadow  /etc/passwd  /etc/shadow >  test\_passwd

![](https://img-blog.csdn.net/2018102509431238?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后就开始用 John 破解密码了。我们可以使用 John 自带的密码字典，位于 /usr/share/john/password.lst ，也可以使用我们自己的密码字典。我这里就用John自带的密码字典为例： john  test\_passwd

如果要使用自己的密码字典的话： john  --wordlist=字典路径    test\_passwd

可以看到，john 已经把我们 test 用户的密码给破解出来了

![](https://img-blog.csdn.net/20181025094745434?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

查看破解信息：john  --show  test\_passwd

这里root用户的密码是之前破解的，所以一共破解了2个用户的密码

![](https://img-blog.csdn.net/20181025095019544?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

相关文章：[Python脚本破解Linux口令](https://blog.csdn.net/qq_36119192/article/details/83514244 "Python脚本破解Linux口令")

### 破解Windows系统密码

首先，执行以下命令通过reg的save选项将注册表中的SAM、System文件导出到本地磁盘。需要管理员权限！

```
reg save hklm\sam sam.hive      
reg save hklm\system system.hive
```


![](https://img-blog.csdnimg.cn/20200205145555319.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 把这两个文件放到John的目录下，执行以下命令将哈希提取到 hash.txt 文件中

```
samdump2 system.hive sam.hive > hash.txt
```


![](https://img-blog.csdnimg.cn/20200927212321433.png)

执行以下命令进行破解

```
john -format=NT hash.txt  #破解,使用john自带的密码字典      
john --show -format=NT hash.txt    #查看破解结果
```


![](https://img-blog.csdnimg.cn/20200927213406743.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200927213454318.png)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)