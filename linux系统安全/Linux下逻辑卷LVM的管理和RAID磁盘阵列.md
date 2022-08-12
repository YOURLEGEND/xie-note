**目录**

[LVM](#t0)

[一：LVM的创建](#t1)

[二：LVM的拉伸](#t2)

[三：LVM的缩小](#t3)

[四：LVM的删除](#t4)

[五：RAID磁盘阵列的添加](#t5)

* * *

LVM
---

LVM(Logical [Volume](https://so.csdn.net/so/search?q=Volume&spm=1001.2101.3001.7020) Manager) 逻辑卷管理器，可以动态调整磁盘容量，提高磁盘管理灵活性。绝大多数分区可以基于LVM创建，但是 /boot 挂载分区不能基于LVM创建。LVM底层文件系统ID 为  **8e**  。

LVM的创建步骤：

1.  在创建磁盘分区的第二步，创建完分区后输入 t 选择分区然后修改分区类型，再输入对应的分区类型ID: 8e 即可。
2.  将磁盘分区初始化为物理卷，pvcreate   /dev/sdb1   ，意思是将sdb1分区初始化为物理卷
3.  将初始化的物理卷加入卷组，vgcreate  myvg    /dev/sdb1  ，意思是将sdb1物理卷组成myvg卷组
4.  在已有的卷组中，创建逻辑卷，lvcreate  -L  1G  -n mylv  myvg ，意思是将myvg卷组中抽出1G的空间建立逻辑卷mylv。然后给逻辑卷分配文件系统  mkfs.ext4  /dev/myvg/mylv
5.  修改 /etc/fstab 文件，重新挂载所有 mount  -a

注：当用一整块硬盘做逻辑卷时，第一步的创建分区和指定分区类型这一步可以省略，直接从第二步开始，将整块硬盘初始化为物理卷。

LVM的组成：

1.  PV(Physical Volume) 物理卷，物理卷可以由整个硬盘也可以是独立分区转化而成。物理卷包括了许多默认大小为4MB的PE(Physical Extent)基本单元
2.  VG(Volumn Group) 卷组，卷组由一个或多个物理卷组成的整体
3.  LV(Logical Volume) 逻辑卷，从卷组中抽出一部分空间，可以建立文件系统

![](https://img-blog.csdn.net/20180904102037823?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

| 功能 | 物理卷PV | 卷组VG | 逻辑卷LV |
| --- | --- | --- | --- |
| 创建 | pvcreate | vgcreate | lvcreate |
| 删除 | pvremove | vgremove | lvremove |
| 增大 |   | vgextend | lvextend    /   lvresize |
| 减小 |   | vgreduce | lvresize |
| 显示 | pvs | vgs | lvs |

### 一：LVM的创建

第一步：创建分区，并将其分区类型修改为LVM

![](https://img-blog.csdn.net/20180904103949227?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

第二步：磁盘分区初始化为物理卷，pvcreate   /dev/sdb1 

![](https://img-blog.csdn.net/20180904104252176?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

第三步：将初始化的物理卷加入卷组，vgcreate  myvg    /dev/sdb1 

![](https://img-blog.csdn.net/20180904104358268?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

第四步：在已有的卷组中，创建[逻辑卷](https://so.csdn.net/so/search?q=%E9%80%BB%E8%BE%91%E5%8D%B7&spm=1001.2101.3001.7020)，lvcreate  -L  1G  -n mylv  myvg  。

![](https://img-blog.csdn.net/20180904104516881?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

使用命令 lvs  查看下我们创建的逻辑卷  mylv

![](https://img-blog.csdn.net/20180904104941370?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

给逻辑卷分配文件系统  mkfs.ext4   /dev/myvg/mylv   ,并且 blkid 查看信息。Linux中默认将所有逻辑卷链接到 /dev/mapper/ 目录下。 /dev/mapper/myvg\_mylv 是 /dev/myvg/mylv 是软连接

![](https://img-blog.csdn.net/2018090410542372?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

第五步：修改/etc/fstab 文件，将逻辑卷挂载信息加进去。

![](https://img-blog.csdn.net/20180904110545523?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

mount   -a  重新挂载，df  -Th 查看，可以看到，我们的 /dev/mapper/myvg-mylv 已经成功挂载上了

![](https://img-blog.csdn.net/20180904110401478?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

假如我们要再建立一个逻辑卷，那么一二三步都可以不用做，直接第四步，在已有的卷组中创建逻辑券(前提是你的卷组还有剩余空间)，然后剩余的步骤一样。我之后又创建了一个xfs文件系统的逻辑卷 mylv2 。

![](https://img-blog.csdn.net/20180904111515235?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 二：LVM的拉伸

拉伸有两条命令： lvextend  、lvresize 。两条命令相同的用法。  
当卷组空间足够分配给逻辑卷时，拉伸LVM分为两步：

1.  拉伸逻辑卷      lvresize / lvextend      \-L  300M    /dev/myvg/mylv
2.  通知文件系统   resize2fs   /dev/myvg/mylv  （EXT文件系统）    xfs\_growfs   /dev/myvg/mylv (XFS文件系统)

第一步：拉伸之前，先用vgs查看卷组中剩余空间的大小，可以看到，我们还剩余1.5G

![](https://img-blog.csdn.net/20180904112006796?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们之前分配的 mylv 是1G ，mylv2是500M ，现在我们把mylv拉伸到1.9G，mylv2拉伸到1.1G。这样，就用完了卷组的空间了

lvresize  -L 1.9G    /dev/myvg/mylv                  
lvresize  -L 1.09G  /dev/myvg/mylv2

![](https://img-blog.csdn.net/2018090411392920?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

第二步：扩展完之后，我们要去通知我们的文件系统，通知完之后，可以看到逻辑卷的空间变大了  
resize2fs     /dev/myvg/mylv                  
xfs\_growfs  /dev/myvg/mylv2                       

![](https://img-blog.csdn.net/20180904114051937?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

可以看到，我们的卷组的空间已经用完了。如果我们还想扩容的话，我们就需要扩大我们的卷组空间了

![](https://img-blog.csdn.net/20180904114233575?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

卷组的扩大：

扩大卷组空间，先新建一个分区，初始化为物理卷，然后加入 myvg 卷组中。可以看到，我们卷组的空间又变大了。接下来，对LVM进行扩容和上面的一样。 vgextend   myvg   /dev/sdb2

![](https://img-blog.csdn.net/20180904114604774?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 三：LVM的缩小

1.  将挂载的文件系统下线   umount        /mnt/lvm
2.  强制磁盘检查                 e2fsck   -f    /dev/myvg/mylv
3.  通知文件系统缩小          resize2fs     /dev/myvg/mylv    100M
4.  缩小逻辑卷                    lvresize     -L   100M   /dev/myvg/mylv
5.  重新挂载                        mount   -a

注：xfs文件系统的逻辑卷不能缩小

![](https://img-blog.csdn.net/20180904144126244?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

注： 当给文件系统通知的是400M ，而实际缩小到600M，这样最后挂载，实际只有400M，也就是浪费了200M  
        当给文件系统通知的600M ,而实际缩小到400M ，这样挂载不上，会报错 。所以给文件系统通知的大小，和实际缩小的大小一定要相同。

### 四：LVM的删除

1.  删除或注释磁盘挂载信息    vim  /etc/fstab ；  将挂载的文件系统下线   umount     /mnt/lvm
2.  删除逻辑卷   lvremove   /dev/myvg/mylv  ；  删除卷组   vgremove myvg  ；删除卷组底层的物理卷  pvremove   /dev/sdb1
3.  删除磁盘分区    fdisk   /dev/sdb  ，一路狂按d和enter
4.  刷新磁盘分区，partprobe  /dev/sdb   如果报错，在确保主机其他文件系统正常的情况下，重启主机  

![](https://img-blog.csdn.net/20180904145949831?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20180904150029659?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![](https://img-blog.csdn.net/20180904150205395?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 五：RAID磁盘阵列的添加

RAID(Redundant  Arrays  of  Inexpensive  Disks)容错式廉价[磁盘阵列](https://so.csdn.net/so/search?q=%E7%A3%81%E7%9B%98%E9%98%B5%E5%88%97&spm=1001.2101.3001.7020)。RAID技术可以通过软件或硬件实现，将多个磁盘整合成为一个较大的磁盘装置，该装置不仅有存储功能，还具有数据保护的功能。RAID具有多个不同的等级，每个等级的对整合后的磁盘实现不同的功能。常用等级有RAID5

![](https://img-blog.csdn.net/20180904164207888?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![](https://img-blog.csdn.net/20180904164230353?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20180904164326504?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

RAID的建立  
1：利用3块硬盘组建RAID5     mdadm  -C  /dev/md0   -n3    -l5    /dev/sd\[bcd\]  。  
     并且查看其状态  mdadm   -D   /dev/md0    或   cat  /proc/mdstat

*      -C 创建阵列存储设备
*      -n 添加磁盘的数量
*      -l RAID的等级

2：创建配置文件  /etc/mdadm.conf ，并且写入配置信息  
3：然后把md0设备建立逻辑卷，修改配置文件 fstab，重新挂载

第一步： 建立RAID存储设备md0，并且查看其状态，记住UUID

![](https://img-blog.csdn.net/20180904170419801?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

第二步：创建配置文件 /etc/mdadm.conf ，并且写入以下的信息

```
ARRAY  /dev/md0  UUID=417c8b54:e78b0978:35ea0e66:63fa5f12
```


第三步：创建逻辑卷，修改配置文件，重新全部挂载。可以看到，我们的基于RAID的逻辑卷已经成功挂载了。

![](https://img-blog.csdn.net/20180904185219407?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```
/dev/myraidvg/myraidlv    /mnt/raid      xfs     defaults   0    0
```


模拟RAID故障： 

模拟其中一块硬盘挂了之后，数据仍然完好无损，然后重新加入一块硬盘。

 先查看 RAID 状态 ，可以看到，三块硬盘都是正常的。  cat /proc/mdstat

![](https://img-blog.csdn.net/20180904190253709?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们先往逻辑卷里写入900M的文件，模拟生产情况下的数据

![](https://img-blog.csdn.net/20180904190700392?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们模拟其中的 sdb 挂了

mdadm   --manage   /dev/md0   --fail   /dev/sdb

*    --fail           将设备设定为出错状态
*    --remove    将设备从阵列中移除
*    --add         添加设备进入阵列

可以看到 ，sdb已经挂了 。但是我们的900M的文件还是依然完好无损

![](https://img-blog.csdn.net/20180904191650661?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

那我们先把挂了的 sdb 移除，然后加上一个好的 sde 。  
madam  --manage  /dev/md0  --remove  /dev/sdb  
madam --manage  /dev/md0  --add  /dev/sde  
可以看到，加上sde后，三个硬盘又组成了RAID。sde会同步其他两块硬盘上的数据 

![](https://img-blog.csdn.net/20180904192253504?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

RAID阵列的停用

1.  umount    /mnt/raid    卸载设备   ;  修改  /etc/fstab 文件中的配置信息
2.  删除阵列中创建的逻辑卷，卷组，物理卷: lvremove，vgremove，pvremove
3.  删除 /etc/mdadm.conf  配置文件，取消设备开机加载阵列
4.  停止 md0:    mdadm  -S  /dev/md0    
5.  验证    cat /proc/mdstat           mdadm -D /dev/md0

![](https://img-blog.csdn.net/20180904193622834?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

注：如果取消挂载设备的时候，显示设备正忙，可以使用 fuser  -mv  /mnt/xxx   看看是哪个进程在使用这个挂载点，然后杀死这个进程即可。

![](https://img-blog.csdn.net/20180907161145208?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

相关文章：[Linux下磁盘分区、卸载和磁盘配额](https://blog.csdn.net/qq_36119192/article/details/82350984)