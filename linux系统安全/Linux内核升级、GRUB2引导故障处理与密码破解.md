**目录**

[内核升级](#t0)

[GRUB2内核启动设置](#t1)

[GRUB2引导菜单加密](#t2)

[GRUB2引导故障及修复](#t3)

[救援模式管理](#t4)

[Rhel7破解root密码](#t5)

* * *

### 内核升级

查看当前内核版本： uname  -r  
查看可升级内核：    yum list  kernel  
升级 kernel：

1.  yum  update  kernel 
2.  rpm  -ivh   kernel-3.10.0-123.1.2.el7.x86\_64.rpm
3.  yum  localinstall  kernel-3.10.0-123.1.2.el7.x86\_64.rpm

### GRUB2内核启动设置

查看已安装kernel版本：cat  /boot/grub2/grub.cfg | grep  -i  'red hat'  
查看当前设置的启动版本：grub2-editenv  list    或者     cat   /boot/grub2/grubenv  
设置kernel启动版本：grub2-set-default  2  
修改grub菜单的超时时间：vim  /etc/default/grub 中的 GRUB\_TIMEOUT=10  
更新引导配置文件：  grub2-mkconfig  -o  /boot/grub2/grub.cfg

![](https://img-blog.csdn.net/20180907102028637?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### GRUB2引导菜单加密

1：通过 grub2-mkpasswd-pbkdf2 命令生成[加密](https://so.csdn.net/so/search?q=%E5%8A%A0%E5%AF%86&spm=1001.2101.3001.7020)密码

![](https://img-blog.csdn.net/20180907110342724?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

:2：打开  /etc/grub.d/00\_header 文件，在末尾追加这几行内容。用户名与密码无关，而且可以系统中没有这个用户名

![](https://img-blog.csdn.net/20180907110530211?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 3：更新配置文件使其生效   grub2-mkconfig  -o  /boot/grub2/grub.cfg

### GRUB2引导故障及修复

GRUB是大多数Linux系统默认使用的引导程序，可以通过启动菜单的方式选择进入不同的操作系统，当配置文件 /boot/grub/grub.conf 文件丢失，或者关键配置出现错误，或者MBR记录中的引导程序遭到破坏时，Linux主机启动后可能只出现“grub>”的提示符，无法完成进一步的系统启动过程。所以我们在工作中，需要对引导的重要文件需要备份。也必须备份。

模拟 /boot/grub2/grub.cfg 文件丢失，重启进入系统，发现进不去，出现 grub >

解决办法：进行手动引导

1.  set  root=(hd0,msdos1)     这个意思就是指定引导分区在第一块硬盘的第一个分区。
2.  linux  /vmlinuz-3.10.0-123.el7.x86\_64     root=/dev/mapper/rhel\_red-root    指定内核将根的位置交给该文件系统
3.  initrd  /initramfs-3.10.0-123.el7.x86\_64.img         加载初始化镜像为硬件加载驱动，须与上一步的内核版本一致
4.  boot   引导进入系统
5.  进入系统后，在硬盘sda上安装grub2，重新覆盖原来的引导   grub2-install  /dev/sda
6.  更新配置文件 grub2-mkconfig  -o  /boot/grub2/grub.cfg，让grub2自己识别不同的系统，自己创建引导

![](https://img-blog.csdn.net/20180907121113772?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20180907135656979?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 救援模式管理

救援模式的作用：

*   可以更改root密码；
*   恢复硬盘、文件系统操作；
*   系统启动不来的时候，只能通过救援模式来启动；

如何进入救援模式：进入系统BIOS设置，VMware中可以电源-->打开电源时进入固件。就可以进入BIOS设置了，将CD调至最前面，然后保存退出即可。

![](https://img-blog.csdn.net/20180910110230631?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20180910110405713?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20180910110447850?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

选 continue，继续，然后会提示根分区被挂载到了 /mnt/sysimages 路径下。  
然后进行磁盘分区强制检查，ext文件系统和xfs文件系统命令不一样

*   fsck -f                ext文件系统磁盘分区
*   xfs\_repair -L      XFS文件系统磁盘分区

然后 chroot  /mnt/sysimages 切换到真根目录下，就可以做我们想做的事了。比如修改密码，修改引导项等等

### hel7破解root密码

1：添加内核参数 rd.break ：  
     重启进入系统，在GRUB2引导的时候，按 e 进入编辑选择列表，找到  initrd16，在这个之前 ，加 rd.break  中断，然后按 ctrl + x进入  
2：内核加载引导系统时，根分区为只读模式挂载，修改模式为读写模式     mount  -o  remount,rw  /sysroot/  
3：切换到根目录   chroot  /sysroot/  
4：修改密码  passwd   
5：修改SELinux的relabel模式    touch   /.autorelabel   ，然后执行两次exit退出

![](https://img-blog.csdn.net/20180906103439483?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20180906105030982?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

相关文章：[Linux启动流程和服务管理(init和systemd)](https://blog.csdn.net/qq_36119192/article/details/82415113) 

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10917 人正在系统学习中