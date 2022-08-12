**目录**

[权限设置](#t0 "权限设置")

[文件夹的NTFS权限](#t1 "文件夹的NTFS权限")

[文件的NTFS权限](#t2 "文件的NTFS权限")

[NTFS权限的应用规则](#t3 "NTFS权限的应用规则")

[文件压缩](#t4 "文件压缩")

[文件加密](#t5 "文件加密")

[磁盘配额](#t6 "磁盘配额")

[卷影副本](#t7 "卷影副本")

[权限设置的应用](#t8 "权限设置的应用")

[遇到的一个权限问题的小bug](#t9 "遇到的一个权限问题的小bug")

[权限问题的实际应用](#t10 "权限问题的实际应用")

* * *

权限设置
----

NTFS文章：[FAT32和NTFS](https://blog.csdn.net/qq_36119192/article/details/82153000#Windows%E4%B8%AD%E7%9A%84%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F "FAT32和NTFS")

在Windows中，NTFS文件系统可以设置文件和目录的权限。存储在 NTFS 分区上的文件和文件夹对用户的访问有安全控制，可以控制用户访问的级别。 当一个用户试图访问一个文件或者文件夹的时候，NTFS 文件系统会检查用户使用的帐户或者账户所属的组是否在此文件或文件夹的访问控制列表（ACL）中。如果存在，则进一步检查访问控制项 ACE，然后根据控制项中的权限来判断用户最终的权限。

![](https://img-blog.csdn.net/20181016112122192?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 文件夹的NTFS权限

文件夹内的文件或文件夹会默认继承上一级目录的权限

*   完全控制：对文件或者文件夹可执行所有操作 
*   修改：可以修改、删除文件或文件夹 
*   读取和执行：可以读取内容，并且可以执行应用程序
*   列出文件夹目录：可以列出文件夹内容，此权限只针对文件夹存在，文件无此权限
*   读取：可以读取文件或者文件夹的内容
*   写入：可以创建文件或者文件夹 
*   特别的权限：其他不常用的权限，比如删除权限的权限

### 文件的NTFS权限

*   完全控制：对文件或者文件夹可执行所有操作
*   修改：可以修改、删除文件或文件夹
*   读取和执行：可以读取内容，并且可以执行应用程序
*   读取：可以读取文件的内容
*   写入：可以修改文件的内容
*   特殊权限：其他不常用的权限，比如删除权限的权限

### NTFS权限的应用规则

*   NTFS 权限具有累加性： 当一个用户属于多个组的时候，用户对某个资源的有效权限是其所有权限来源的总和
*   “拒绝”权限会覆盖所有其他的权限： 虽然用户对某个资源的有效权限是其所有权限来源的总和，但是只要其中有一个权限被设为拒绝访问，则用户将无法访问该资源
*   NTFS 权限的继承： 当用户设置文件夹的权限后，位于该文件夹下添加的子文件夹与文件，默认会自动继承该文件夹的权限

文件压缩
----

文件、文件夹的压缩可以减少它们占用磁盘的空间，但是文件的大小是不变化的。压缩可以对文件、文件夹或整个卷进行。

NTFS 文件系统的压缩过程和解压过程对用户是完全透明的，压缩前和压缩后的文件在使用上没有不同。

**复制或移动文件对压缩状态的影响**

| 操作 | 同一NTFS分区 | 不同NTFS分区 | FAT32分区 |
| --- | --- | --- | --- |
| 复制 | 继承目标文件压缩状态 | 继承目标文件压缩状态 | 压缩状态丢失 |
| 移动 | 保留原文件（夹）压缩状态 | 继承目标文件压缩状态 | 压缩状态丢失 |

文件压缩的启用：右键文件，属性，高级，然后会显示文件压缩了，[加密](https://so.csdn.net/so/search?q=%E5%8A%A0%E5%AF%86&spm=1001.2101.3001.7020)后的文件显示是蓝色的

![](https://img-blog.csdn.net/20181016164517534?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20181016164542787?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

文件原来的大小是1.18MB，占用空间也是1.18MB。压缩后文件大小还是1.18MB，只不过占用空间变成了1.01MB。

![](https://img-blog.csdn.net/20181016164611747?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

文件加密
----

*   利用“加密文件系统 EFS（Encrypting File System）”提供文件加密功能。文件或文件夹经过加密后，只有当初将其加密的用户或者经过授权的用户能够读取，因此可以增强文件的安全性。
*   只有 NTFS 磁盘内的文件和文件夹才可以被加密，FAT32 磁盘并没有该功能。
*   如果将文件复制或移动到非 NTFS 磁盘内，则该文件会解密。 文件压缩与加密无法共存。
*   如果要对已经压缩的文件进行加密，则该文件会自动解压缩；如果要对已经加密的文件进行压缩，则该文件会自动解密

文件加密的启用：右键文件，属性，高级，然后会显示文件加密了，加密后的文件显示是绿色的

![](https://img-blog.csdn.net/20181016161027942?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20181016161244508?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

一般情况下其他用户是看不了你的加密文件的，如果想要让其他的用户可以看你的加密文件，你需要把你的证书导出来，然后其他用户再将你的证书导入进去，就可以看你的加密文件了。

证书的导出：打开IE浏览器-->工具-->Internet选项-->内容-->证书-->导出

证书的导入：打开IE浏览器-->工具-->Internet选项-->内容-->证书-->导入

磁盘配额
----

在 Windows 中，用户可利用磁盘配额功能来控制和跟踪每个用户可用的磁盘空间。下面列出磁盘配额的特性：

*   磁盘配额是针对单一用户来控制与跟踪的。 
*   只有 NTFS 磁盘才支持磁盘配额功能。
*   磁盘配额是以文件和文件夹的所有权进行计算的。
*   磁盘配额的计算不考虑文件压缩的因素。
*   每个 NTFS 磁盘分区的磁盘配额是独立计算的，不论这几个 NTFS 分区是否在同一个硬盘内。
*   系统管理员默认不会受到磁盘配额的限制。

磁盘配额的启用：右键-->属性-->然后找到配额

![](https://img-blog.csdn.net/20181016160249781?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

当启用了磁盘配额后，其他用户显示的磁盘的容量就是配额的大小

![](https://img-blog.csdn.net/20181016160142280?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

卷影副本
----

卷影副本，也称快照，是存储在 Data Protection Manager (DPM) 服务器上的副本的时间点副本。副本是文件服务器上单个卷的受保护共享、文件夹和文件的完整时间点副本。共享文件夹的卷影副本提供位于共享资源上的实时文件副本。通过使用共享文件夹的卷影副本，用户可以查看在过去某个时刻存在的共享文件和文件夹。在Windows Server版本上的系统都有卷影副本的功能，在普通家用版本上不提供该功能。

卷影副本只能对于整个盘符进行，而不能对于文件或目录做卷影副本。

访问文件的以前版本或卷影副本非常有用，原因是用户可以：

*   恢复被意外删除的文件。如果用户意外删除了某个文件，则可以打开前一版本，然后将其复制到安全的位置 
*   恢复意外覆盖的文件。如果用户意外覆盖了某个文件，则可以恢复该文件的前一版本
*    在处理文件的同时对文件版本进行比较。当用户希望检查一个文件的两个版本之间发生的更改时，可以查看到以前的版本

卷影副本的启用：右键盘符-->找到卷影副本-->点击是，然后就进入了下面的界面，然后就可以对相应的盘符做卷影副本了

![](https://img-blog.csdn.net/20181016154624394?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

权限设置的应用
-------

### 遇到的一个权限问题的小bug

不知道这个小bug是不是windows权限问题的bug，反正这个bug违反了权限累加性。

例：我现在是以Administrators组登录的一个用户，我创建了一个文件test。目前我对这文件拥有所有的权限，包括读写修改等等所有权。然后我现在就是想让其他用户只能看这个文件，但是修改不了这个文件。所以我就需要对文件的权限进行修改。

我把 Authenticated Users 和Users 用户组的权限改成了只有读取和执行的权限，而没有其他任何的权限。

注：这里修改 Authenticated Users组的权限的时候，要先把这个组的默认继承权限给取消勾选了，然后再修改其权限

![](https://img-blog.csdn.net/20181016194724803?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![](https://img-blog.csdn.net/20181016194851274?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

效果是达到了，其他的用户只能看这个文件，但是修改不了这个文件。但是，有一个很大的bug，那就是文件的创建者也不能修改这个文件，而且这个创建者是属于Administrators用户组的，按理说Administartors用户组对该文件完全控制。但是事实就是只要你把Authenticated Users组的权限改成只有读取和执行的话，所有人都只能读取而不能修改了。

这个bug对于目录同样存在，目录的话你只要修改了Authenticated Users组的权限为读取和列出文件夹内容，那么所有的其他用户只能列出该目录的内容，Administrators组内的用户除了可以列出内容外还可以创建文件夹和移动文件或文件夹到该目录，但是创建不了文件！！！

以上 bug 发生的前提是取消了 Authenticated Users 组的默认继承权限，然后修改其权限为只能读取和执行。或者就是删除Authenticated Users权限都会发生这个bug。

### 权限问题的实际应用

只允许用户读自己提交的文件

某软件学院学生平时作业都是提交电子版文档到文件服务器的 HomeWork 文件夹中，为了防止其他学生相互抄袭作业，需要设置 HomeWork 文件夹的 NTFS 权限，达到以下目的：

*   学生能够打开 HomeWork 文件夹 
*   学生能够在 HomeWork 文件夹中提交文件 
*   不允许用户在 HomeWork 中创建文件夹
*   学生对自己提交的文件有完全控制权，即能够删除、修改、读取 
*   学生能够看到其他学生提交的文件，但不能打开，不能删除和修改

首先，因为要让其他学生可以提交文件到文档服务器，所以学生要有可以登录到系统的账号。又因为Authenticated Users组和Users组包含了可以登录的用户，所以先把这两个组的权限给删掉。但是又不能直接把这两个组给删除，必须先取消其继承权限，然后删除。

然后给所有的学生创建一个stu组，用来管理学生用户的。编辑stu组的权限，让其拥有列出文件夹和创建文件的权限，只应用于该文件夹

![](https://img-blog.csdn.net/20181016224400930?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后因为学生要可以对自己的文件进行删除修改等操作，所以就是学生要对自己的文件拥有完全控制操作。所以我们就可以编辑CREATOR\_OWNER的权限，让其拥有完全控制权，应有于该文件夹内的文件

![](https://img-blog.csdn.net/20181016224510612?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

还有就是要让管理员可以查看学生提交的作业，所以针对管理员用户 xie 给予权限，使其对该文件夹以及文件夹内的文件拥有完全控制权

![](https://img-blog.csdn.net/20181016224642295?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

这是该文件夹的权限截图

![](https://img-blog.csdn.net/20181016224733289?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)