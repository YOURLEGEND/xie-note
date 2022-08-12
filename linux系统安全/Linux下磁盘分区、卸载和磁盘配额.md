**目录**

[一：查看磁盘信息](#t0)

[二：Linux磁盘分区](#t1)

[三：Linux分区的卸载](#t2)

[四：Linux磁盘配额](#t3)

* * *

### **一：查看磁盘信息**

 fdisk : 这个命令是磁盘[分区表](https://so.csdn.net/so/search?q=%E5%88%86%E5%8C%BA%E8%A1%A8&spm=1001.2101.3001.7020)操作工具，fdisk能将磁盘分区，同时也能为每个分区指定分区类型，总的来说，fdisk就是磁盘工具

*   fdisk -l : 查看硬盘分区表。 从这里我们可以看出，此系统中挂载了一块硬盘 sda。这块硬盘有三个分区，分别为 sda1,sda2和sda5。sda1是主分区，可以引导启动，分区类型是Linux；sda2是extend扩展分区，sda5是逻辑分区，sda5的Type类型是swap，所以可知sda5是交换分区。**sda=sda1+sda2+sda5**

![](https://img-blog.csdn.net/20180829191054361?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

*   df -Th : 显示已挂载的磁盘文件系统。

![](https://img-blog.csdn.net/20180904123409302?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

*   cat  /proc/partitions : 显示已识别的所有分区

![](https://img-blog.csdn.net/20180904084018799?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 二：Linux磁盘分区

在windows下硬盘分区很简单，有专门的磁盘管理工具，还有众多的第三方软件可以对硬盘进行分区，都是图形化的，操作简单方便。所以这里我们就不多说。

今天主要将的是在Linux下用 fdisk 这个命令来对硬盘进行分区的。Linux下对硬盘分区大致可以分为七步。

1.  添加并识别硬件设备       fdisk  -l    查看是否识别  或者   ls  -l  /dev/sdb 查看是否有这个设备
2.  创建分区并识别分区       fdisk  /dev/sdb        按照步骤一步步对硬盘分区，然后保存退出
3.  制作文件系统                 mkfs.ext4  /dev/sdb1       对sdb硬盘的第一个分区制作ext4文件系统
4.  制作磁盘标签                 e2lable  /dev/sdb1           newpart  给分区起一个别名
5.  创建挂载点                    mkdir  /mnt/sdb1              在/mnt 目录下新建一个挂载点
6.  将分区信息写入文件系统分区表       修改/etc/fstab文件，将分区信息写入，保存退出
7.  挂载文件系统                 mount -a   读取fstab文件，挂载文件中所有文件系统。  df -Th  查看是否挂载上

第一步：我系统原来有一块硬盘 /dev/sda 大小60G，后来我再加了一个20G的硬盘，可以看到，已经识别到了： /dev/sdb

![](https://img-blog.csdn.net/20180903110431770?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

第二步：接下来我要对 sdb 进行分区,使用 fdisk   /dev/sdb 命令 ，进入 fdisk 模式。这时，有好多选项：

*     n 创建新的磁盘分区
*     d 删除已存在的磁盘分区
*     l 查看支持的磁盘分区类型
*     t 转换分区类型
*     p 查看磁盘的分区信息
*     w 保存修改并退出
*     q 不保存直接退出

我们输入 l 查看一下支持哪些磁盘分区类型，可以看到，支持这么多种分区类型。

![](https://img-blog.csdn.net/20180903145900276?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们输入 n  创建新的磁盘分区。

然后让我们选择Partition Type 分区类型，默认是 primary 主分区，还有 extend 扩展分区。我们选择默认，直接回车即可。

然后让我们选择分区数字，范围1-4。默认是从1开始的。如果上一步选的是扩展分区，则选择4。我们直接回车默认即可。

然后让我们选择扇区起始编号，默认是2048，我们也选默认，回车即可。

然后就让我们输入这个分区的大小了。这里我们输入的是 +5G ,即第一块分区的大小是5G。

这样，我们就已经建好了一个标准的Linux格式的分区类型了。

![](https://img-blog.csdn.net/20180903153555145?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们再建四个分区，一个swap分区类型的sdb2分区，一个FAT32分区类型的sdb3分区。一个extend扩展分区sdb4，扩展分区里面有一个逻辑分区 sdb5。

sdb2和sdb3都是主分区，建立方法和第一个差不多，只不过建成后输入 t 然后修改分区类型，再输入对应的分区类型ID即可。

![](https://img-blog.csdn.net/20180903151617229?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

扩展分区sdb4和逻辑分区sdb5建立方法有点不同。建立sdb4的时候，选择分区类型 e ，即扩展分区。然后一路回车即可。就是把剩下的所有空间都给sdb4。sdb4只是相当于一个容器，并不能真正的使用，要在其中建立逻辑分区才可以使用。所以我们新建逻辑分区sdb5，一路回车即可。就是把扩展分区的容量都给了逻辑分区sdb5了。然后输入 p 查看已分配的分区。输入 w 保存退出！

![](https://img-blog.csdn.net/20180903152346552?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

识别新分区： partprobe   /dev/sdb  

cat   /proc/partitions   查看已识别的分区。这里我们可以看到已经识别到了我们刚刚建立的所有分区。

![](https://img-blog.csdn.net/20180903153316345?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

第三步：创建好了新分区后，我们就要对新分区分配文件系统了，不同的分区类型有不同的文件系统，使用的指令也不一样。扩展分区是一个容器，不用分配文件系统。sdb2交换分区类型是用命令 mkswap。其他几个分区类型用的是 mkfs 命令，sdb1和sdb5用的是标准的ext4文件系统，sdb3用的是vfat文件系统。(本来sdb5想用xfs文件系统，结果kali不支持)

![](https://img-blog.csdn.net/20180903154959419?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

第四步(可选)：我们就可以给分区制作LABEL标签了，也就是别名，这一步可做可不做。只不过在最后修改fstab文件的时候可以用别名表示分区。e2label  /dev/sdb1   newpart   。然后使用 blkid 可以查看已经制作好的文件系统的信息。

![](https://img-blog.csdn.net/20180903155517453?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

第五步: 我们就去/mnt/ 文件下建立[挂载](https://so.csdn.net/so/search?q=%E6%8C%82%E8%BD%BD&spm=1001.2101.3001.7020)点，分别是 sdb1 、sdb3 、sdb5的挂载点。sdb2 交换分区不需要挂载点，sdb4扩展分区也不需要挂载点。

![](https://img-blog.csdn.net/20180904090043223?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

第六步: 我们就可以去修改 /etc/fstab 文件了，把我们的分区信息写入进去，保存退出，这里要注意的是swap分区类型的挂载点就是swap，文件系统也是swap。这里有6个字段：

1.   第一列表示待挂载的文件系统，挂载方式可以是设备路径（/dev/sdb1），可以是LABEL，可以是UUID
2.   第二列是挂载点，表示设备使用后，从那个接入点使用磁盘空间
3.   第三列是文件系统类型，制作文件系统时的格式写在这个位置
4.   第四列是挂载时的磁盘参数，默认包含可读可写等
5.   第五列是是否对磁盘做dump备份
6.   第六列是是否对磁盘做fsck检查

![](https://img-blog.csdn.net/20180903160257626?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

第七步: 就是挂载文件系统了，使用mount -a  ,就是挂载fstab文件中的所有文件系统。

然后使用 df -Th  查看已挂载的所有文件系统。

swap分区sdb2不显示在这里，因为它没有挂载点。

extend扩展分区sdb4也不会显示在这里，因为他仅仅是一个分区，并没有分配文件系统。

![](https://img-blog.csdn.net/20180903160649858?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

swap分区的作用是创建一个虚拟内存，开启虚拟内存的指令为 swapon -a，将开启所有fstab文件中的swap分区  
关闭使用swapoff，如果只是关闭一个分区，swapoff   /dev/sdb2，关闭所有 swapoff -a  
查看虚拟内存空间变化可以使用 free -m   ，查看当前的虚拟内存可以用 swapon -s

可以看到，开启了我们创建的虚拟内存后，虚拟内存数量多了一个，虚拟内存空间也变大了

![](https://img-blog.csdn.net/20180903211726865?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

写了一个脚本实现自动分区，目前只支持分配主分区和标准的linux文件系统(ext4/xfs)的分区

```
#! /bin/bash      
# Author:谢公子      
# Date:2018-10-13      
# Function:对硬盘进行分区,得到一个标准的linux文件系统(ext4/xfs)的主分区      
cat /proc/partitions > old      
read -p "请输入你要分区的硬盘(写绝对路径，如：/dev/sda)：" A      
if [ -e $A ];then      
  echo "true"      
else      1
  echo "该设备不存在！！"      1
  exit      1
fi      1
read -p "请输入你要创建的磁盘分区类型(这里只能是主分区，默认按回车即可):" B      1
read -p "请输入分区数字，范围1-4，默认从1开始，默认按回车即可：" C      1
read -p "请输入扇区起始表号，默认按回车即可：" D      1
read -p "请输入你要分区的分区大小(格式：如 +5G )：" E      1
fdisk $A << EOF      1
n      1
p      2
$C      2
$D      2
$E      2
w      2
EOF      2
echo "一个标准的linux文件系统的分区已经建立好！！"      2
partprobe $A      2
echo "-------------------------------"      2
cat /proc/partitions      2
cat /proc/partitions > new      3
F=`diff new old | grep "<" | awk '{print $5}'`      3
echo "-------------------------------"      3
echo $F      3
echo "你想对新分区设定什么类型的文件系统？有以下选项："      3
echo "A：ext4文件系统"      3
echo "B：xfs文件系统"      3
read -p "请输入你的选择：" G      3
case $G in      3
        a|A)      3
           mkfs.ext4 /dev/$F      4
           echo "该分区将被挂载在 "/mnt/$F" 下"       4
           m=`ls /mnt/|grep $F | wc -l`      4
           if [ $m -eq 0 ];then      4
            mkdir /mnt/$F      4
           fi      4
           n=`cat /etc/fstab | grep /dev/$F| wc -l`      4
           if [ $n -eq 0 ];then      4
              echo "/dev/$F     /mnt/$F     ext4         defaults          0      0" >> /etc/fstab      4
           else      4
              sed -i '/^\/dev\/$F/c\/dev/$F     /mnt/$F     ext4         defaults          0      0' /etc/fstab      5
           fi      5
           mount -a      5
           df -Th      5
;;      5
        b|B)      5
           mkfs.xfs -f /dev/$F      5
           echo "该分区将被挂载在 "/mnt/$F" 下"       5
           m=`ls /mnt/|grep $F | wc -l`      5
           if [ $m -eq 0 ];then      5
              mkdir /mnt/$F      6
           fi      6
           n=`cat /etc/fstab | grep /dev/$F | wc -l`      6
           if [ $n -eq 0 ];then      6
              echo "/dev/$F     /mnt/$F      xfs       defaults          0      0" >> /etc/fstab      6
           else      6
              sed -i '/^\/dev\/$F/c\/dev/$F     /mnt/$F     xfs         defaults          0      0' /etc/fstab      6
           fi      6
           mount -a      6
           df -Th      6
;;      7
        *)      7
           echo "你的输入有误！！"      7
esac
```


### 三：Linux分区的卸载

现在把我们上面建立的几个分区都给卸载了

第一步：先卸载所有已挂载的分区。先查看哪些分区已经挂载上 df -Th ，再依次卸载。最后关闭虚拟分区，因为系统原来就分配有虚拟分区，所以我们只关闭我们自己新建的虚拟分区 swapoff   /dev/sdb2 。再最后查看是否已卸载完

![](https://img-blog.csdn.net/20180904081516473?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

如果umount的时候，显示设备正忙。可以使用 fuser  -mv  /mnt/sdbx  来查看哪个进程在使用这个设备，然后kill掉即可。

第二步：修改 /etc/fstab 文件，把我们这几个分区的信息都给删除了或者注释掉

![](https://img-blog.csdn.net/2018090408201382?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

第三步：进入/dev/sdb 里面， fdisk  /dev/sdb  ，一路按 d ，把创建的所有分区给删除了，然后 p 查看是否删除干嘛 ，w 保存。

![](https://img-blog.csdn.net/20180904082615471?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

刷新一下 sdb 磁盘的分区信息  partprobe  /dev/sdb   。

然后查看识别到的分区信息  cat   /proc/partitions ，可以看到已经删除干净了。

![](https://img-blog.csdn.net/20180904082926278?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 四：Linux磁盘配额

管理员可以为用户所能使用的磁盘空间进行配额限制，每一用户只能使用最大配额范围内的磁盘空间。既可对用户存储文件数量做限制，也可对用户存储文件大小做限制

磁盘配额的分为五步：

1.  挂载的同时需要为文件系统添加支持配额的选项   usrquota , grpquota
2.  配额检查 quotacheck   -augcv ，在分区中生成配额文件 aquota.user和 aquota.group
3.  为用户建立配额信息 edquota  -u/g   用户名/组名
4.  开启/关闭配额功能    quotaon  -a  、 quotaoff -a
5.  查看配额信息    quota  -u 、 quota  -g  、 repquota  -a

这里我用Red hat7做实验，在Red hat7上新建了一个sdb1的分区，用sdb1做磁盘配额实验。

第一步：修改 /etc/fstab文件，找到sdb1所对应的挂载信息，在磁盘参数defaults后面加上 usrquota，grpquota，意思是给这块分区添加用户配额和组配额的功能。

![](https://img-blog.csdn.net/20180903224628539?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

设备启用了配额后，需要将文件系统重载。

umount /mnt/sdb1 ；mount -a （或直接重新挂载  mount -o remount   /mnt/sdb1）。

然后查看配额信息是否已经添加进去了  mount | grep  sdb1

![](https://img-blog.csdn.net/20180903224722510?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

第二步：执行配额检查 quotacheck  -augcv  

*   \-a 扫描所有支持配额的分区
*   \-u 扫描磁盘并计算用户所占用的文件数
*   \-g 扫描磁盘并计算组所占用的文件数
*   \-c 创建配额文件 aquota.user 和 aquota.group
*   \-v 显示详细信息

执行后，会检查所有支持磁盘配额的分区，如果分区之前有配置配额，先关闭，再次开启会有配置文件被更新。  
如果是第一次创建磁盘配额，那么会提示之前没有任何配额文件，并且会在分区中生成配额文件 aquota.user 和 aquota.group

![](https://img-blog.csdn.net/20180903224854578?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

第三步：为用户或组建立配额信息  
edquota  -u   xie   编辑用户 xie 的配额  
edquota  -g   IT    编辑组IT的配额  
一共有六个字段  
 Filesystem                   blocks       soft       hard      inodes      soft     hard  
 /dev/sdb1                       0             80M      100M       0             8         10  
 前三个字段表示针对文件空间限制，后三个字段表示针对文件数量限制

1.   第一个字段 blocks 表示用户在文件系统中已占用的空间统计，一般我们不做修改
2.   第二个字段 soft 表示用户创建文件时，文件空间的警告阈值，超过数值则提示（软限制一般为提醒，小于硬限制即可，不设置或者超过硬限制都无意义）
3.   第三个字段 hard 表示用户的空间限制，最大使用的磁盘空间
4.   第四个字段 inodes 表示用户已创建文件的数量统计，一般我们不做修改
5.   第五个字段 soft 表示用户在文件系统中创建文件的数量警告阈值，超过数量则提示
6.   第六个字段 hard 表示用户创建文件的数量限制，最大创建文件数量

这个截图的意思是限制用户xie在此磁盘中最大使用空间100M ，并且使用超过80M就提示。最多使用创建10个文件，并且超过8个文件就提示。

![](https://img-blog.csdn.net/20180903225142360?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

第四步：开启 / 关闭配额 功能 

如果开启的时候显示设备或资源忙，就先关闭所有配额，再重新打开。

quotaon  -a  表示开启支持配额功能的所有分区     
quotaoff  -a   表示关闭支持配额功能的所有分区   

![](https://img-blog.csdn.net/2018090323115815?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)   

第五步：查看配额信息

查看配额信息的方式有两种。

*    quota -u 用户名 只查看用户的配额信息
*    quota -g 组名 只查看组的配额信息
*    repquota 设备 查看设备内的配额信息

注：只有当用户在这个分区上有数据时，才可以看到配额信息，否则只显示none

![](https://img-blog.csdn.net/20180903231016362?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

第六步：验证配额

配置好了磁盘配额后，我们就去验证了，切换到 xie 用户 ，提前给测试分区权限。

我们复制文件到磁盘上，每次复制30M ，当复制到第三次的时候，达到了90M>80M ，所以就提示我们磁盘紧张了。当第四次复制时，就提示已经超过磁盘配额了，第四次只能复制10M的文件过去。

![](https://img-blog.csdn.net/20180904075512741?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我在上面创建了8个文件，当创建第9个的时候提示我磁盘紧张了。当创建第11个，直接提示我磁盘限制，创建不了第11个了。

![](https://img-blog.csdn.net/20180904080455129?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

相关文章： [Linux下性能监控与进程管理](https://blog.csdn.net/qq_36119192/article/details/82191051)

                   [Linux下逻辑卷LVM的管理和RAID磁盘阵列](https://blog.csdn.net/qq_36119192/article/details/82378871)

                   [硬盘分区、系统引导、文件系统入门](https://mp.csdn.net/postedit/82153000)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10917 人正在系统学习中