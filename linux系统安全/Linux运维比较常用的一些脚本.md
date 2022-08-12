**目录**

[一、根据PID过滤进程所有信息](#t0)

[二、根据进程名过滤进程信息](#t1)

[三、根据用户名查询该用户的相关信息](#t2)

[四、加固系统的一些配置](#t3)

[五：实现磁盘分区的](#t4)

[六、使用一整块硬盘创建逻辑卷](#t5)

[七、将一块硬盘分区，然后分区设置为虚拟卷](#t6)

* * *

### 一、根据PID过滤进程所有信息

```
#! /bin/bash      
# Author:谢公子      
# Date:2018-10-10      
# Function: 根据用户输入的PID，过滤出该PID所有的信息      
read -p "请输入要查询的PID: " P      
n=`ps -aux| awk '$2~/^'$P'$/{print $11}'|wc -l`      
if [ $n -eq 0 ];then      
 echo "该PID不存在！！"      
 exit      1
fi      1
echo "--------------------------------"      1
echo "进程PID: $P"      1
echo "进程命令：`ps -aux| awk '$2~/^'$P'$/{print $11}'`"      1
echo "进程所属用户: `ps -aux| awk '$2~/^'$P'$/{print $1}'`"      1
echo "CPU占用率：`ps -aux| awk '$2~/^'$P'$/{print $3}'`%"      1
echo "内存占用率：`ps -aux| awk '$2~/^'$P'$/{print $4}'`%"      1
echo "进程开始运行的时刻：`ps -aux| awk '$2~/^'$P'$/{print $9}'`"      1
echo "进程运行的时间：`ps -aux| awk '$2~/^'$P'$/{print $10}'`"      1
echo "进程状态：`ps -aux| awk '$2~/^'$P'$/{print $8}'`"      2
echo "进程虚拟内存：`ps -aux| awk '$2~/^'$P'$/{print $5}'`"      2
echo "进程共享内存：`ps -aux| awk '$2~/^'$P'$/{print $6}'`"      2
echo "--------------------------------"
```


![](https://img-blog.csdn.net/20181013160232671?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 二、根据进程名过滤进程信息

会显示出该进程名包含的所有线程

```
#! /bin/bash      
# Author:谢公子      
# Date：2018-10-10      
# Function: 根据输入的程序的名字过滤出所对应的PID，并显示出详细信息，如果有几个PID，则全部显示      
read -p "请输入要查询的进程名：" NAME      
N=`ps -aux | grep $NAME | grep -v grep | wc -l`    ##统计进程总数      
if [ $N -le 0 ];then      
  echo "该进程名没有运行！"      
fi      1
i=1      1
while [ $N -gt 0 ]      1
do      1
  echo "进程PID: `ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $2}'`"      1
  echo "进程命令：`ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $11}'`"      1
  echo "进程所属用户: `ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $1}'`"      1
  echo "CPU占用率：`ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $3}'`%"      1
  echo "内存占用率：`ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $4}'`%"      1
  echo "进程开始运行的时刻：`ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $9}'`"      1
  echo "进程运行的时间：`  ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $11}'`"      2
  echo "进程状态：`ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $8}'`"      2
  echo "进程虚拟内存：`ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $5}'`"      2
  echo "进程共享内存：`ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $6}'`"      2
  echo "***************************************************************"      2
  let N-- i++      2
done
```


![](https://img-blog.csdn.net/2018101316052779?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 三、根据用户名查询该用户的相关信息

```
#! /bin/bash      
# Author:谢公子      
# Date:2018-10-12      
# Function：根据用户名查询该用户的所有信息      
read -p "请输入要查询的用户名：" A      
echo "------------------------------"      
n=`cat /etc/passwd | awk -F: '$1~/^'$A'$/{print}' | wc -l`      
if [ $n -eq 0 ];then      
echo "该用户不存在"      1
echo "------------------------------"      1
else      1
  echo "该用户的用户名：$A"      1
  echo "该用户的UID：`cat /etc/passwd | awk -F: '$1~/^'$A'$/{print}'|awk -F: '{print $3}'`"      1
  echo "该用户的组为：`id $A | awk {'print $3'}`"      1
  echo "该用户的GID为：`cat /etc/passwd | awk -F: '$1~/^'$A'$/{print}'|awk -F: '{print $4}'`"      1
  echo "该用户的家目录为：`cat /etc/passwd | awk -F: '$1~/^'$A'$/{print}'|awk -F: '{print $6}'`"      1
  Login=`cat /etc/passwd | awk -F: '$1~/^'$A'$/{print}'|awk -F: '{print $7}'`      1
  if [ $Login == "/bin/bash" ];then      1
  echo "该用户有登录系统的权限！！"      2
  echo "------------------------------"      2
  elif [ $Login == "/sbin/nologin" ];then      2
  echo "该用户没有登录系统的权限！！"      2
  echo "------------------------------"      2
  fi      2
fi
```


![](https://img-blog.csdn.net/20181013160747810?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 四、加固系统的一些配置

```
#! /bin/bash      
# Author:谢公子      
# Date:2018-10-11      
# Function:对账户的密码的一些加固      
read -p  "设置密码最多可多少天不修改：" A      
read -p  "设置密码修改之间最小的天数：" B      
read -p  "设置密码最短的长度：" C      
read -p  "设置密码失效前多少天通知用户：" D      
sed -i '/^PASS_MAX_DAYS/c\PASS_MAX_DAYS   '$A'' /etc/login.defs      1
sed -i '/^PASS_MIN_DAYS/c\PASS_MIN_DAYS   '$B'' /etc/login.defs      1
sed -i '/^PASS_MIN_LEN/c\PASS_MIN_LEN     '$C'' /etc/login.defs      1
sed -i '/^PASS_WARN_AGE/c\PASS_WARN_AGE    '$D'' /etc/login.defs       1
echo "已对密码进行加固，新用户不得和旧密码相同，且新密码必须同时包含数字、小写字母，大写字母！！"      1
sed -i '/pam_pwquality.so/c\password    requisite     pam_pwquality.so try_first_pass local_users_only retry=3 authtok_type=  difok=1 minlen=8 ucredit=-1 lcredit=-1 dcredit=-1' /etc/pam.d/system-auth       1
echo "已对密码进行加固，如果输入错误密码超过3次，则锁定账户！！"      1
n=`cat /etc/pam.d/sshd | grep "auth required pam_tally2.so "|wc -l`      1
if [ $n -eq 0 ];then      2
sed -i '/%PAM-1.0/a\auth required pam_tally2.so deny=3 unlock_time=150 even_deny_root root_unlock_time300' /etc/pam.d/sshd      2
fi       2
echo  "已设置禁止root用户远程登录！！"      2
sed -i '/PermitRootLogin/c\PermitRootLogin no'  /etc/ssh/sshd_config       2
read -p "设置历史命令保存条数：" E      2
read -p "设置账户自动注销时间：" F      2
sed -i '/^HISTSIZE/c\HISTSIZE='$E'' /etc/profile      2
sed -i '/^HISTSIZE/a\TMOUT='$F'' /etc/profile       3
echo "已设置只允许wheel组的用户可以使用su命令切换到root用户！"      3
sed -i '/pam_wheel.so use_uid/c\auth            required        pam_wheel.so use_uid ' /etc/pam.d/su      3
n=`cat /etc/login.defs | grep SU_WHEEL_ONLY | wc -l`      3
if [ $n -eq 0 ];then      3
echo SU_WHEEL_ONLY yes >> /etc/login.defs      3
fi       3
echo "即将对系统中的账户进行检查...."      3
echo "系统中有登录权限的用户有："      4
awk -F: '($7=="/bin/bash"){print $1}' /etc/passwd      4
echo "********************************************"      4
echo "系统中UID=0的用户有："      4
awk -F: '($3=="0"){print $1}' /etc/passwd      4
echo "********************************************"      4
N=`awk -F: '($2==""){print $1}' /etc/shadow|wc -l`      4
echo "系统中空密码用户有：$N"      4
if [ $N -eq 0 ];then      4
 echo "恭喜你，系统中无空密码用户！！"      4
 echo "********************************************"      5
else      5
 i=1      5
 while [ $N -gt 0 ]      5
 do      5
    None=`awk -F: '($2==""){print $1}' /etc/shadow|awk 'NR=='$i'{print}'`      5
    echo "------------------------"      5
    echo $None      5
    echo "必须为空用户设置密码！！"      5
    passwd $None      5
    let N--      6
 done      6
 M=`awk -F: '($2==""){print $1}' /etc/shadow|wc -l`      6
 if [ $M -eq 0 ];then      6
  echo "恭喜，系统中已经没有空密码用户了！"      6
 else      6
echo "系统中还存在空密码用户：$M"      6
 fi      6
fi       6
echo "即将对系统中重要文件进行锁定，锁定后将无法添加删除用户和组"      7
read -p "警告：此脚本运行后将无法添加删除用户和组！！确定输入Y，取消输入N；Y/N：" i      7
case $i in      7
      [Y,y])      7
            chattr +i /etc/passwd      7
            chattr +i /etc/shadow      7
            chattr +i /etc/group      7
            chattr +i /etc/gshadow      7
            echo "锁定成功！"      7
;;      7
      [N,n])      8
            chattr -i /etc/passwd      8
            chattr -i /etc/shadow      8
            chattr -i /etc/group      8
            chattr -i /etc/gshadow      8
            echo "取消锁定成功！！"      8
;;      8
       *)      8
            echo "请输入Y/y or  N/n"      8
esac
```


![](https://img-blog.csdn.net/2018101316132251?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 五：实现磁盘分区的

只支持分配主分区和标准的linux文件系统(ext4/xfs)的分区

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


### 六、使用一整块硬盘创建逻辑卷

```
#!/bin/bash      
# Author:谢公子      
# Date:2018-10-12      
# Function:使用一整块硬盘创建LVM逻辑卷      
read -p "请输入你要做成逻辑卷的硬盘(写绝对路径，如：/dev/sda)：" path      
if [ -e $path ];then      
  echo "true"      
else      
  echo "该设备不存在！！"      1
  exit      1
fi      1
pvcreate $path      1
echo "该硬盘已做成物理卷！"      1
vgcreate myvg $path      1
echo "该物理卷已加入卷组 myvg 中"      1
vgs      1
free=`vgs| awk '$1~/myvg/{print}'|awk '{print $6}'`      1
echo "该物理卷剩余的空间大小为：$free "      1
read -p "请输入你要创建逻辑卷的大小(如：1G)：" repy2      2
lvcreate -L $repy2 -n mylv myvg      2
echo "已成功创建逻辑卷mylv"      2
echo "------------------------"      2
lvs      2
echo "------------------------"      2
echo "你想对新分区设定什么类型的文件系统？有以下选项："      2
echo "A：ext4文件系统"      2
echo "B：xfs文件系统"      2
read -p "请输入你的选择：" repy3      2
case $repy3 in      3
        a|A)      3
           mkfs.ext4 /dev/myvg/mylv      3
           echo "该分区将被挂载在 "/mnt/mylv" 下"       3
           m=`ls /mnt/|grep mylv | wc -l`      3
           if [ $m -eq 0 ];then      3
            mkdir /mnt/mylv      3
           fi      3
           echo "/dev/myvg/mylv     /mnt/mylv     ext4         defaults          0      0" >> /etc/fstab      3
           mount -a      3
           df -Th      4
;;      4
        b|B)      4
           mkfs.xfs -f /dev/myvg/mylv      4
           echo "该分区将被挂载在 "/mnt/mylv" 下"       4
           m=`ls /mnt/|grep mylv | wc -l`      4
           if [ $m -eq 0 ];then      4
              mkdir /mnt/mylv      4
           fi      4
           echo "/dev/myvg/mylv     /mnt/mylv      xfs       defaults          0      0" >> /etc/fstab      4
           mount -a      5
           df -Th      5
;;      5
        *)      5
           echo "你的输入有误！！"      5
esac
```


### 七、将一块硬盘分区，然后分区设置为虚拟卷

```
#! /bin/bash      
# Author:谢公子      
# Date:2018-10-13      
# Function:新建一个分区，并做成逻辑卷      
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
read -p "请输入你要划分为逻辑卷的分区盘符(默认回车即可)：" Z      1
fdisk $A << EOF      1
n      2
p      2
$C      2
$D      2
$E      2
t      2
$Z      2
8e      2
p      2
w      2
EOF      3
echo "一个标准LVM的分区已经建立好！！"      3
partprobe $A      3
echo "-------------------------------"      3
cat /proc/partitions      3
cat /proc/partitions > new      3
F=`diff new old | grep "<" | awk '{print $5}'`      3
echo "-------------------------------"      3
echo $F      3
pvcreate /dev/$F      3
echo "该硬盘已做成物理卷！"      4
n=`vgs | grep myvg |wc -l`      4
if [ $n -eq 0 ];then      4
   vgcreate myvg /dev/$F      4
   echo "该物理卷已加入卷组myvg中"      4
else      4
   vgextend myvg /dev/$F      4
   echo  "该物理卷已加入卷组myvg中"      4
   vgs      4
   free=`vgs| awk '$1~/myvg/{print}'|awk '{print $7}'`      4
   echo "该卷组剩余的空间大小为：$free "      5
   lvs      5
   exit      5
fi      5
vgs      5
free=`vgs| awk '$1~/myvg/{print}'|awk '{print $6}'`      5
echo "该卷组剩余的空间大小为：$free "      5
read -p "请输入你要创建逻辑卷的大小(如：1G)：" repy2      5
lvcreate -L $repy2 -n mylv myvg      5
echo "已成功创建逻辑卷mylv"      5
echo "------------------------"      6
lvs      6
echo "------------------------"      6
echo "你想对新分区设定什么类型的文件系统？有以下选项："      6
echo "A：ext4文件系统"      6
echo "B：xfs文件系统"      6
read -p "请输入你的选择：" G      6
case $G in      6
        a|A)      6
           mkfs.ext4 /dev/myvg/mylv      6
           echo "该分区将被挂载在 "/mnt/$F" 下"       7
           m=`ls /mnt/|grep $F | wc -l`      7
           if [ $m -eq 0 ];then      7
            mkdir /mnt/$F      7
           fi      7
           echo "/dev/myvg/mylv     /mnt/$F     ext4         defaults          0      0" >> /etc/fstab      7
           mount -a      7
           df -Th      7
;;      7
        b|B)      7
           mkfs.xfs -f /dev/myvg/mylv      8
           echo "该分区将被挂载在 "/mnt/$F" 下"       8
           m=`ls /mnt/|grep $F | wc -l`      8
           if [ $m -eq 0 ];then      8
              mkdir /mnt/$F      8
           fi      8
           echo "/dev/myvg/mylv     /mnt/$F      xfs       defaults          0      0" >> /etc/fstab      8
           mount -a      8
           df -Th      8
;;      8
        *)      9
           echo "你的输入有误！！"      9
esac
```


更多脚本：[https://www.jb51.net/article/54488.htm](https://www.jb51.net/article/54488.htm)

相关文章：[应急响应系统之 Linux 主机安全检查](https://mp.weixin.qq.com/s?__biz=MzI5MDQ2NjExOQ==&mid=2247489990&idx=1&sn=db37670d24a97e33b58d30e8f653a909&scene=21#wechat_redirect)