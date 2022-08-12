**目录**

[伪协议](#t0)

[file:// 协议](#t1)

[php://filter 伪协议](#t2)

[php://input 伪协议](#t3)

* * *

伪协议
---

什么是伪协议呢？如果你安装了QQ或者TIM，在地址栏输入如下形式，便会调用Timwp.exe会进行解析处理。

```
tencent://Message/?uin=xxxxxx&websiteName=q-zone.qq.com&Menu=yes
```


这种形如标准协议HTTP、FTP的自定义协议叫做伪协议。

例如，在E盘下我们有一个 1.txt 文件，内容为：hello,word!

![](https://img-blog.csdnimg.cn/20191029163434461.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在网站下有一个 test.php，文件内容如下：

```
<?php      
    include($_GET['filepath']);      
?>
```


### file:// 协议

```
http://127.0.0.1/test.php?filepath=file:///e:/1.txt
```


![](https://img-blog.csdnimg.cn/20191029163733335.png)

### php://filter 伪协议

该伪协议读取源代码并进行base64编码输出，不然会直接当做php代码执行就看不到源代码内容了。

```
http://127.0.0.1/test.php?filepath=php://filter/read=convert.base64-encode/resource=file://e:/1.txt
```


![](https://img-blog.csdnimg.cn/2019102916391931.png)

### php://input 伪协议

该伪协议可以将post的数据交给服务器当成文件接收

test.php文件内容如下

```
<?php      
    echo file_get_contents($_GET['filepath'],'r');      
?>
```


### ![](https://img-blog.csdnimg.cn/20191029170829433.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)