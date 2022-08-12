**目录**

[一：配置本地yum源](#t0)

[二：配置网络yum源](#t1)

* * *

更新源可以获取最新的软件信息，以更新您的系统

Redhat7配置源
----------

YUM(Yellow dog  Updater Modified): [yum](https://so.csdn.net/so/search?q=yum&spm=1001.2101.3001.7020)是Redhat系列系统基于RPM包构建的软件更新机制，可以自动解决 rpm 包之间的依赖关系，所有软件包由集中的yum软件仓库提供。其理念是使用一个中心仓库(repository)管理一部分甚至一个distribution的应用程序相互关系，根据计算出来的软件依赖关系进行相关的升级、安装、 删除等操作，减少了Linux用户一直头痛的dependencies的问题。 可以同时配置多个资源库(Repository)，简洁的配置文件(/etc/yum.conf)，自动解决增加或删除rpm包时遇到的依赖性问题,保持与RPM数据库的一致性。

yum的配置文件在  /etc/yum.repos.d/ 下面，必须以 .repo 结尾！

### 配置本地yum源

配置本地yum源是通过本地映射光盘挂载到系统中，然后将yum的配置文件中的 baseurl 指向挂载的目录即可。

首先在虚拟机的这个配置中，勾选使用iso映射文件，然后选中我们iso文件所在的位置即可。

![](https://img-blog.csdn.net/20180830155311781?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

在虚拟机系统中，我们的 iso 文件是 /dev/sr0 设备,系统会默认把我们的iso文件挂载到 /run/media/$username/ 目录下。但是现在我们要将iso文件挂载到 /mnt/cdrom 下。如果mnt目录下没有cdrom，那么我们自己新建一个。

![](https://img-blog.csdn.net/20180830161338337?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

挂载有两种方式挂载

一： 一次性挂载，重启完系统后失效

```
mount /dev/sr0  /mnt/cdrom
```


![](https://img-blog.csdn.net/20180830161516777?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

二： 永久挂载

我们的设备的挂载信息在 /etc/fstab 里面，所以我们要想永久挂载，需要修改 fstab 文件。用 vim 打开该文件，我们可以看到有三行信息，是其他分区的挂载信息，所以我们现在要把我们的光盘的挂载信息添加过去。

![](https://img-blog.csdn.net/20180830161754487?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

添加如下我们光盘的挂载信息过去

```
/dev/sr0        /mnt/cdrom      iso9660   defaults    0    0
```


![](https://img-blog.csdn.net/20180830165502617?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

添加完后，保存退出。

然后执行  mount   -a  。这个命令的意思是挂载fstab文件中的所有文件系统。可以看到，我们已经挂载好了。

![](https://img-blog.csdn.net/20180830165826500?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

挂载好iso文件之后，我们去修改源的配置文件 /etc/yum.repos.d/ ，它默认有一个文件，我们把它删除，然后自己新建以 .repo 结尾的文件，用vim编辑器打开。输入以下的配置，保存。

```
[name]               #括号中的名称为仓库源名称，通常为字母和数字，必须填写      
name=my new repo     #对yum的描述，可写可不写      
baseurl=file:///mnt/cdrom    #baseurl表示声明yum可以管理并使用的rpm包路径,必须填写      
enabled=1            #enabled表示当前仓库是否开启，1为开启，0为关闭，此项不写默认为开启      
gpgcheck=0           #gpgcheck表示安装rpm包时，是否基于公私钥对匹配包的安全信息，1表示开启，      
                     #0表示关闭，此项不写默认为验证
```


然后执行  yum clean all   ，清空缓存信息。 

yum  list | wc -l   统计列出所有包的数量，如果列出了，说明yum源配置完成。这里可以看出有4405个包。

![](https://img-blog.csdn.net/20180830171044867?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 配置网络yum源

由于Red Hat Enterprise Linux7的更新包只对注册的用户生效，所以需要自己手动改成Centos的更新包，Centos几乎和和[Redhat](https://so.csdn.net/so/search?q=Redhat&spm=1001.2101.3001.7020)是一样的，所以无需担心软件包的兼容问题。

首先查看redhat7系统本身安装的yum软件包 ，redhat本身安装了6个关于yum的软件包，我们需要把它给卸载！

```
rpm  -qa | grep yum
```


![](https://img-blog.csdn.net/20180830171728816?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

卸载这些软件包的指令   

```
rpm -qa | grep yum | xargs rpm -e --nodeps   #不检查依赖关系，直接卸载
```


![](https://img-blog.csdn.net/20180830185339209?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后重新安装centos的yum软件包 ，把软件包放到统一的文件夹里，然后安装，安装完后可以看到centos安装包已经安装好。这里我看其他很多人的教程给的centos的yum的安装包的链接都是不可用了，后来我自己到国外的网站找了几个可用的安装包。

链接: [https://pan.baidu.com/s/10IrBjM5j\_TJT0Qc2aW64Cg](https://pan.baidu.com/s/10IrBjM5j_TJT0Qc2aW64Cg)   提取码: i4pe

```
rpm -ivh *              # 安装该目录下所有rpm包      
rpm -qa | grep yum      # 查询是否安装好
```


![](https://img-blog.csdn.net/20180830185827166?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

安装完centos的yum软件包后，就可以去修改 /etc/yum.repos.d/ 下的文件了，这里和配置本地yum源修改一样

```
[base]      
name= yum repo      
baseurl=http://mirrors.aliyun.com/centos/7/os/$basearch/      
enabled=1      
gpgcheck=0
```


配置完保存退出，清除yum缓存，查看yum中的包有多少，这里可以看到有11056个，比本地yum源的四千多个多了不少！

![](https://img-blog.csdn.net/20180830191255844?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### Kali更新源

相关文章：[Linux系统中安装软件的几种方式](https://blog.csdn.net/qq_36119192/article/details/82866329)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10917 人正在系统学习中