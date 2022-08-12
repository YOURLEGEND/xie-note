**目录**

[Provider提供程序](#t0 "Provider提供程序")

[FileSystem提供程序](#t1 "FileSystem提供程序")

[注册表提供程序](#t2 "注册表提供程序")

* * *

Provider提供程序
------------

在[PowerShell](https://so.csdn.net/so/search?q=PowerShell&spm=1001.2101.3001.7020)中，对于文件系统，如 c:\\abc、d:\\abc，我们可以通过Get-ItemProperty，Get-Item， Get-ChildItem等命令访问。访问注册表，证书存储，环境变量，函数，WSMan其实都可以像访问文件系统一样，使用Get-ItemProperty，Get-Item， Get-ChildItem等命令访问。这一切都得益于，PowerShell为这些资源注册了一个**Provider（中文翻译为：提供程序）** 。Provider提供的是数据，提供的数据可以是文件、[环境变量](https://so.csdn.net/so/search?q=%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F&spm=1001.2101.3001.7020)、注册表、证书存储等。

查看当前注册的所有提供程序，可以看到当前注册的提供程序有：Registry、Alias、Environment、FileSystem、Variable、Certificate、WSMan。这些提供程序都有各自不同的功能。有些时候，当我们启用了某个功能的时候，可能会新增提供程序。

```
Get-PSProvider
```


![](https://img-blog.csdnimg.cn/20210107095558498.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

可以看到不同的Provider拥有不同的Capabilities能力。

*   ShouldProcess：这部分提供程序支持 -WhatIf 和 -Confirm 参数。确保我们在正式执行脚本之前可以对他们进行测试。
*   Filter：在cmdlet中 操作提供程序的数据时，支持-Filter参数。
*   Credentials：该提供程序允许使用可变更的凭据连接数据存储。这也是 -Credentials 参数的作用。
*   Transactions：该提供程序支持事务，也就是允许你在该提供程序中将多个变更为一个原子操作进行提交或全部回滚。

### FileSystem提供程序

Windows文件系统主要由3种对象组成：

*   磁盘驱动器
*   文件夹
*   文件

磁盘驱动器是最上层的对象，包含文件夹和文件。文件夹是一种容器对象，它可以包含文件以及其他文件夹。文件不是一种容器对象，它处于层级的末尾。但是在PowerShell中，不管文件还是文件夹，都被叫做 项(Item)。所以，一个项(Item)里面，可以包含其他Item( ChildItem子项，可以是文件或文件夹)。每个项都会存在其对应的属性 ItemProperty  。比如，一个文件项可能有最后写入的时间、是否只读等属性。我们可以使用Clear、Copy、Get、Move、New、Romve、Rename、Set等动词操作这些项以及操作它们的属性。

我们执行以下命令查看Powershell中的PSDrive。PSDrive可以通过一个特定的提供程序连接到某些存储数据的介质。这和Windows资源管理器中类似，本质上是创建了一个驱动器映射。但是由于PSDrive使用了提供程序，除了可以连接磁盘以外，还能关联更多的数据存储介质。如下，可以看到FileSystem提供程序提供了对磁盘 C、D、E的访问。

```
Get-PSDrive
```


![](https://img-blog.csdnimg.cn/2021010709501181.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
创建test文件夹      
New-Item test -ItemType Directory      
创建test.txt文件      
New-Item test.txt -ItemType File
```


![](https://img-blog.csdnimg.cn/20210108104739583.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 注册表提供程序

```
Set-Location -Path HKCU:      
Set-Location -Path .\Software\      
Get-ChildItem      
Set-Location -Path .\Microsoft\      
Set-Location -Path .\Windows\      
Get-ChildItem      
可以看到有 EnableWindowColorization 的键值，值为0，以下命令将其键值修改为1      
Set-ItemProperty -Path .\DWM\ -PSProperty EnableWindowColorization -Value 1
```


![](https://img-blog.csdnimg.cn/2021010810035764.png)

![](https://img-blog.csdnimg.cn/20210108100253169.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210108103339174.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

具体某个键值的值

![](https://img-blog.csdnimg.cn/20210108103704146.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2021010810353860.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)