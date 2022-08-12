**目录**

[VMware ESXi登录](#t0)

[vSphere Web Client](#t1)

[Login | Appliance Management](#t2)

[vRealize Operations Manager](#t3)

* * *

![](https://img-blog.csdnimg.cn/20201026171728884.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

红蓝对抗过程中，在[内网](https://so.csdn.net/so/search?q=%E5%86%85%E7%BD%91&spm=1001.2101.3001.7020)中经常会碰到Vmware相关的产品。如：

*   ******VMware ESXi登录******
*   ******vRealize Operations Manager******
*   ******Login | Appliance Management******
*   ******vSphere Web Client******

官方文档：[https://docs.vmware.com/cn/VMware-vSphere/5.5/com.vmware.vsphere.vcenterhost.doc/GUID-13DC79ED-5611-489C-AFC8-4910761F859E.html](https://docs.vmware.com/cn/VMware-vSphere/5.5/com.vmware.vsphere.vcenterhost.doc/GUID-13DC79ED-5611-489C-AFC8-4910761F859E.html)

首先我们了解一下什么是[VMware](https://so.csdn.net/so/search?q=VMware&spm=1001.2101.3001.7020) vSphere?

        VMware [vSphere](https://so.csdn.net/so/search?q=vSphere&spm=1001.2101.3001.7020) 是VMware公司推出一套服务器虚拟化解决方案，是业界领先且最可靠的虚拟化平台。vSphere将应用程序和操作系统从底层硬件分离出来，从而简化了 IT操作。您现有的应用程序可以看到专有资源，而您的服务器则可以作为资源池进行管理。因此，您的业务将在简化但恢复能力极强的 IT 环境中运行。

### ******VMware ESXi登录******

     ESXi是VMware推出的一款优秀的服务器级别的虚拟机。它与我们常用的虚拟机不同的是，日常使用的虚拟机是需要依赖于一个操作系统的。比如在Windows上使用Vmware，或者Linux上使用Virtualbox。而ESXi不依赖于任何操作系统，它本身就可以看作一个操作系统，然后可以在它上面安装系统。

![](https://img-blog.csdnimg.cn/20201026152036512.png)

**80端口**

![](https://img-blog.csdnimg.cn/20201026152107222.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**443端口**

这个界面是EXSI登录界面，输入的账号密码为ESXI安装时设置的账号密码。用户名默认为 root。密码为安装ESXI时设置的密码，格式如下：

*   第一个为大写字母
*   接下来为小写字母
*   然后再加一串数字
*   最后一位为一个符号
*   密码长度不少于八位

如果在参数配置页面，点击Reset System Configuration的话，则无需密码即可登录。

![](https://img-blog.csdnimg.cn/20201026160625426.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

https://10.xx.xx.xx/ui/#/login

![](https://img-blog.csdnimg.cn/20201026152140968.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### ******vSphere Web Client******

VCenter服务器开放了389端口，可以通过389端口开放来判断是否是VCenter

![](https://img-blog.csdnimg.cn/20201026152343276.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

该界面是VCenter管理界面：https://10.x.x.x/vsphere-client/?locale=zh\_CN&csp 。登录该界面后，可以管理所有的虚拟机

![](https://img-blog.csdnimg.cn/20201026154515477.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在vSphere5.5之前，是使用vSphere Client来管理虚拟机。但从vSphere 5.5开始，改成使用vSphere Web Client管理虚拟机了。另外在vCenter Server 5.5之前，vSphere Client的管理端口是9443，而在vCenter Server 5.5之后，vSphere Web Client管理端口为443。

![](https://img-blog.csdnimg.cn/20201026170119357.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### ******Login | Appliance Management******

这个界面是VCenter虚拟机的管理界面：https://10.x.x.x:5480/login.html

![](https://img-blog.csdnimg.cn/20201026154624740.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

登录成功后，可以管理VCenter所在的虚拟机。

![](https://img-blog.csdnimg.cn/20201026170731914.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### ******vRealize Operations Manager******

vRealize Operations Manager是Vmware 官方提供的针对Vmware[虚拟化](https://so.csdn.net/so/search?q=%E8%99%9A%E6%8B%9F%E5%8C%96&spm=1001.2101.3001.7020)平台的一套运维管理解决方案，通过它可以看到整个虚拟化环境的整体运行情况，潜在风险，以及优化建议等，同时可以提供一些虚拟化运行配置、容量、优化建议等等报告。

vRealize Operations Manager 老站点存在 struts2 漏洞。

[https://10.xx.xx.xx/ui/login.action](https://10.xx.xx.xx/ui/login.action)

![](https://img-blog.csdnimg.cn/20201026154731397.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

相关文章：[安装ESXi](https://blog.csdn.net/huaidan1469/article/details/100032963)

                  [手把手教你从ESXI部署到vSphere web Client管理控制](https://www.cnblogs.com/bixiaoyu/p/10716921.html)

                  [vmware vrealize operations manager 7部署总结](https://blog.csdn.net/zuimeng520/article/details/97929916)