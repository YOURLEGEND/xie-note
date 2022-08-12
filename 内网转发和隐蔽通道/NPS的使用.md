**目录**

[服务端的安装](#t0 "服务端的安装")

[客户端](#t1 "客户端")

[Profile连接](#t2 "Profile连接")

* * *

NPS是一款好用的[内网穿透](https://so.csdn.net/so/search?q=%E5%86%85%E7%BD%91%E7%A9%BF%E9%80%8F&spm=1001.2101.3001.7020)工具，不过相比于FRP还是稍微比较麻烦一点，原因在于NPS的服务端需要安装。

以下是NPS的使用：

工具下载地址：[https://github.com/ehang-io/nps/releases](https://github.com/ehang-io/nps/releases "https://github.com/ehang-io/nps/releases")

官方说明文档：[Document](https://ehang-io.github.io/nps/#/api "Document")

### 服务端的安装

首先查看服务器的[架构](https://so.csdn.net/so/search?q=%E6%9E%B6%E6%9E%84&spm=1001.2101.3001.7020)，下载对应版本的NPS。我的服务器是x86\_64的，所以下载 linux\_amd64\_server.tar.gz

![](https://img-blog.csdnimg.cn/2020080317030397.png)

上传到服务器，解压，安装

```
tar -xvzf linux_amd64_server.tar.gz      
./nps install
```


![](https://img-blog.csdnimg.cn/20200803170534726.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

修改/etc/nps/conf/nps.conf 配置文件的 http\_proxy\_port 和 https\_proxy\_port 端口，防止端口冲突。至于 bridg\_port 和 web\_port 保持默认即可。注意，修改web管理端的账号密码！！

![](https://img-blog.csdnimg.cn/20200814233930800.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**启动**

```
./nps start      
./nps stop
```


![](https://img-blog.csdnimg.cn/20200803172007238.png)

登录web管理端，默认是8080端口。

![](https://img-blog.csdnimg.cn/20200803172059500.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**新建一个客户端**

![](https://img-blog.csdnimg.cn/2020080317240616.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200803172618942.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**新建一个socks5代理**

![](https://img-blog.csdnimg.cn/20200803172827590.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200803172938400.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 客户端

*   客户端windows使用windows\_amd64\_client.tar.gz
*   客户端linux用linux\_amd64\_client.tar.gz

执行以下命令进行无配置文件链接

```
Windows：      
    npc.exe -server=ip:8024 -vkey=P@ssword      
Linux：      
    ./npc -server=xx.xx.xx.xx:8024 -vkey=P@ssword
```


![](https://img-blog.csdnimg.cn/20200803173420400.png)

![](https://img-blog.csdnimg.cn/20200803174514693.png)

### Profile连接

![](https://img-blog.csdnimg.cn/20200803173517812.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200803173541679.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关文章：[内网穿透工具FRP的使用](https://xie1997.blog.csdn.net/article/details/99310312 "内网穿透工具FRP的使用")  
 

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中