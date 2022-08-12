**目录**

[一：用户和组信息的查看](#t0)

[查看用户信息](#t1)

[查看密码信息](#t2)

[查看组信息](#t3)

[特殊组wheel](#t4)

[二：用户和组信息的管理](#t5)

[用户管理](#t6)

[组管理](#t7)

[三：文件权限](#t8)

[文件权限的查看](#t9)

[文件权限的修改](#t10) 

[ACL控制权限 setfacl  、 getfacl](#t11)

[Umask、Suid、Sgid、粘滞位](#t12)

* * *

> 前言：在linux中一切都是文件（文件夹和硬件设备是特殊的文件)，如果有可能尽量使用文本文件。文本文件是人和机器能理解的文件，也成为人和机器进行交流的最好途径。由于所有的配置文件都是文本，所以你只需要一个最简单的编辑器就可以修改。由于修改文本文件如此简单，所以Linux系统本身肯定要加以规范。这就引出了用户(组)和权限这2个概念。而这2个概念的引入，完美的保证了Linux的安全性，同时没有添加复杂性。由于一切皆为文件。所以Linux引入了3个文件来管理用户（组）， /etc/passwd存放用户信息，/etc/shadow存放用户密码信息，/etc/group存放组信息，然后在文件系统中的每个文件的文件头里面添加了用户和文件之间的关系信息。

用户、组、文件间有三种关系

*   用户和文件的关系只有2种， 拥有和不拥有。
*   组和文件的关系只有2种，  拥有和不拥有。
*   用户和组的关系只有2种， 属于和不属于。

将这三种关系叠加，用户和文件的最终关系可以归纳为3类

*   用户拥有该文件
*   用户属于某个组，某个组拥有该文件（即用户通过属于某组来拥有该文件） 
*   用户不拥有该文件

一：用户和组信息的查看
-----------

在Linux下，用户分为三类：超级用户(root)、普通用户、程序用户。

*   超级用户：UID=0
*   程序用户：Rhel5/6，UID=1-499；                Rhel7，UID=1-999
*   普通用户：Rhel5/6，UID=500-65535；        Rhel7，UID=1000-60000

有三个命令可以查看用户的相关信息

```
cat  /etc/passwd         #查看用户信息      
cat  /etc/shadow         #查看用户的密码信息      
cat  /etc/group          #查看用户的组信息
```


### 查看用户信息

```
cat /etc/passwd   #/etc/passwd默认权限为644，其最小权限为444
```


![](https://img-blog.csdn.net/20181011114854545?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

用户信息的显示有7个字段

1.    字段1：用户名  --> root
2.    字段2：密码占位符  --> x （这里都是用x代替）
3.    字段3：uid，用户id  --> 0
4.    字段4：gid ，组id --> 0
5.    字段5：用户描述信息  --> root
6.    字段6：家目录  -->  /root
7.    字段7：登录 shell   （用户登陆shell ，当为/bin/bash表示可以登陆，/sbin/nologin表示不被授权登陆）

注：一般来说，只有 root 用户的 uid 是为0的。如果黑客把一个普通用户的 uid 修改为0的话，那么他只要以普通用户的用户名和密码登录，系统就会自动切换到root用户。所以，系统加固的时候一定要过滤出有哪些用户的UID为0

使用脚本查看用户信息

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


### ![](https://img-blog.csdn.net/20181012220512711?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 查看密码信息

```
cat  /etc/shadow     #shadow默认权限为600，最小权限为400
```


![](https://img-blog.csdnimg.cn/20181108082850402.png)

密码信息的显示有9个字段

1.    字段1：用户名
2.    字段2：通过sha-512加密(二次加密，在经过第一次加密后，第二次加入随机数再次加密)的密码
3.    字段3：最后一次修改密码距离1970年1月1日的天数间隔
4.    字段4：密码最短有效期
5.    字段5：密码最长有效期
6.    字段6：密码过期前几天进行警告
7.    字段7：账户过期后，被锁定的天数
8.    字段8：账号失效时间距离1970年1月1日的天数间隔
9.    字段9：未分配功能

字段2是用户的密码位，如果是 \* 表示该用户禁用，!! 表示用户密码未初始化，如果为空，表示空密码的

![](https://img-blog.csdn.net/20181011102748365?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

注：借助chage指令，可以修改用户的密码策略，也可通过编辑 /etc/shadow (不建议)  
比如：chage   -l   bob，查看用户bob的密码策略  
           chage  -M  90  bob，将用户bob的密码有效期修改为90天

脚本实现修改用户的密码策略

```
#！ /bin/bash      
# Author:谢公子      
# Date: 2018-10-12      
# Function: 实现对用户密码策略的设定，如密码最长有效期等      
read -p  "设置密码最多可多少天不修改：" A      
read -p  "设置密码修改之间最小的天数：" B      
read -p  "设置密码最短的长度：" C      
read -p  "设置密码失效前多少天通知用户：" D      
sed -i '/^PASS_MAX_DAYS/c\PASS_MAX_DAYS   '$A'' /etc/login.defs      1
sed -i '/^PASS_MIN_DAYS/c\PASS_MIN_DAYS   '$B'' /etc/login.defs      1
sed -i '/^PASS_MIN_LEN/c\PASS_MIN_LEN     '$C'' /etc/login.defs      1
sed -i '/^PASS_WARN_AGE/c\PASS_WARN_AGE   '$D'' /etc/login.defs      1
echo "已设置好密码策略......"
```


![](https://img-blog.csdn.net/20181013091021314?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 查看组信息

```
cat /etc/group
```


![](https://img-blog.csdnimg.cn/20181108083128258.png)

组信息的显示有四个字段

1.    字段1：组名称 --> root
2.    字段2：组密码占位符  --> x
3.    字段3：gid --> 0
4.    字段4：组成员

注：一个用户只能有一个主要组，最多可以有31个附加组。主要组是用户创建文件时默认的所有组，附加组主要用于权限管理。不论用户属于哪个组，用户都能拥有该组的权限

### 特殊组wheel

在Linux中有一个特殊组wheel，wheel组就类似于一个管理员的组。在linux中，即使我们有系统管理员root的权限，也不推荐用root用户登录。一般情况下用普通用户登录就可以了，在需要root权限执行一些操作时，再su登录成为root用户。但是，任何人只要知道了root用户的密码，就都可以通过su命令来登录为root用户--这无疑为系统带来了安全隐患。所以，将普通用户加入到wheel组中，被加入的这个普通用户就成了管理员组内的用户了，然后可以修改配置文件使得只有wheel组内的用户可以切换到root用户。

相关文章：[设置只有指定用户能使用su命令切换到root用户](https://blog.csdn.net/qq_36119192/article/details/82906799#%E8%AE%BE%E7%BD%AE%E5%8F%AA%E6%9C%89%E6%8C%87%E5%AE%9A%E7%94%A8%E6%88%B7%E8%83%BD%E4%BD%BF%E7%94%A8su%E5%91%BD%E4%BB%A4%E5%88%87%E6%8D%A2%E5%88%B0root%E7%94%A8%E6%88%B7)

二：用户和组信息的管理
-----------

### 用户管理

新建用户系统会做三件事

1.   新建用户时，系统会将 /etc/skel 中的目录及文件拷贝到新建用户的家目录中
2.   在 /var/spool/mail 中，新建用户名的邮箱 
3.   在 /etc下的 passwd 、shadow 、group文件中，增加用户信息

添加用户时指定参数：

*   添加用户时，使用 -g 指定新建用户的组，使用 -u 指定用户uid
*   \-G 参数可以指定新建用户的附加组
*   使用  -s   /sbin/nologin  指定创建的用户没有登录系统的权限
*   还可以使用 -M 参数，指定创建的用户不在home目录下创建家目录
*   还可以使用 -d 参数 ，指定其家目录

```
useradd  tom     #新建tom用户，其配置默认      
useradd  -u 2000  -g  james   bob   #新建bob用户，将其uid设置为 2000，将其所在的组设置为  james ( 不修改默认是bob )，前提是james组已经存在      
useradd  -G root tom   #新建tom用户，指定其附加组为root      
useradd  -s /sbin/nologin tom #新建tom用户，但是不让tom用户有登录系统的权限，默认是 /bin/bash ，没登录权限的话是 /sbin/nologin      
useradd  -d /test  tom     #新建tom用户，指定其家目录为 /test (默认是 /home/tom)      
useradd  -M tom   #新建tom用户，但是不在home目录下新建目录(默认创建新用户会在home目录下新建同名的家目录)       
useradd xie;echo 'xie:123456'|chpasswd  #添加用户xie，密码为 123456
```


以下这条命令直接生成一个具有root权限的用户：venus，密码为：123qwe 。前提是这条命令的执行需要root权限。

```
useradd -p `openssl passwd -1 -salt 'user' 123qwe` -u 0 -o -g root  -G root -s /bin/bash -d /home/user venus
```


 注：用户创建时，默认的属性（比如UID，GID，是否创建家目录，创建邮箱等）都是通过/etc/login.defs文件控制的，修改此文件的属性，会影响以后创建的所有用户。也可以创建用户时指定参数修改，这样只对当前创建用户有效

**删除用户** ：

```
userdel  -r  james  #删除用户一定记得加 -r 参数 ！！
```


1.  不加 -r 参数，只删除 passwd、shadow 和 group 文件中的用户信息，/home 目录下的文件不删除，/var/spool/mail/ 下的文件不删除
2.  加  -r 参数，删除 passwd、shadow 和 group 文件中的用户信息，同时删除用户的家目录和邮箱

**修改账户密码** ：

```
passwd  james   #给james用户设置密码
```


    当前用户为 root 时：

1.  不需要知道当前的密码
2.  设置新密码时，不需要遵循密码要求

    当前用户为普通用户时：

1.  需要知道当前密码
2.  设置新密码时，必须遵循密码要求（1.不能少于8个字符，2.满足复杂度要求）

修改账户属性: 

```
usermod   参数   james
```


1.    -s 修改用户的登陆shell       usermod   -s   /sbin/nologin    james 
2.    -L 账户锁定  (可以通过 passwd -S  账户名 查看账户的状态)
3.    -U 解锁账户
4.    -g  修改账户所在组      例： 将bob所在组改成james：usermod  -g  james   bob   
5.    -G  给账户添加附加组  例：给bob添加一个附加组john：usermod  -G  john  bob      ；                    从附加组john中删除用户bob： gpasswd -d  bob  john
6.    -a  默认情况下，当用户已经存在附加组时，再添加附加组则会把之前的附加组给替换了，加 -a 参数，则不替换原来的附加组，意味着该用户可以有多个附加组。

```
[root@Redhat ]# id james      
uid=1000(james) gid=2002(james) 组=2002(james)       
[root@Redhat ]# usermod -g aaa james ; id james;  //修改用户的主组为aaa      
uid=1000(james) gid=2000(aaa) 组=2000(aaa)       
[root@Redhat ]# usermod -G xie james ;id james;   //给用户添加附加组 xie       
uid=1000(james) gid=2000(aaa) 组=2000(aaa),1000(xie)       1
[root@Redhat ]# usermod -G xiao james ;id james;  //给用户添加附加组xiao，并且如果原来有附加组的话替换原来的附加组      1
uid=1000(james) gid=2000(aaa) 组=2000(aaa),2001(xiao)       1
[root@Redhat ]# usermod -aG xie james;id james;  //给用户添加附加组 xie ,并且不替换原来的附加组      1
uid=1000(james) gid=2000(aaa) 组=2000(aaa),1000(xie),2001(xiao)
```


锁定和解锁用户 ：

*   锁定用户： usermod  -L  xie    或   passwd  -l   xie
*   解锁用户： usermod  -U  xie   或   passwd  -u  xie
*   查看用户状态：passwd  -S xie

![](https://img-blog.csdn.net/20181011162203314?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

注：虽然 usermod 和 passwd 这两个命令都可以锁定和解锁用户，但是还是有区别的。区别之一就是passwd命令操作完后会有提示。还有一个区别就是 passwd 的权限比 usermod 大，使用 usermod 锁定的用户可以用 passwd 来解锁，但是使用 passwd 锁定的用户不能用 usermod 来解锁

### 组管理

添加组 ： groupadd  xie

1.    -g, --gid                         为新组使用 GID，例 groupadd  -g 2000  xie   创建新组xie，并且gid设置为2000
2.    -K, --key                        不使用 /etc/login.defs 中的默认值
3.    -o, --non-unique            允许创建有重复 GID 的组
4.    -p, --password               为新组使用此加密过的密码
5.    -r, --system                    创建一个系统账户

删除组: groupdel    xie

1.    -r , --remove                            删除主目录和邮件池

注：只能删除附加组，而不能删除主组！！！！

修改组的属性： groupmod  xie

  -g, --gid GID                 将组 ID 改为 GID  
  -n, --new-name            改名为 NEW\_GROUP  
  -o, --non-unique          允许使用重复的 GID

```
groupmod  -g  2000 bob       //将bob组的GID修改为2000      
groupmod  -n  aaa   bob      //将bob组的名字改为aaa      
groups    bob                //查看bob所属的所有组
```


修改组中的用户：groupmems 

```
groupmems   -a  john  -g  xie     将用户john加到xie组中      
groupmems   -d  john  -g  xie     将用户john从xie组中移除 或 gpasswd -d  john  xie
```


三：文件权限
------

### 文件权限的查看

使用 ls -lh 可以查看文件的具体信息，其中包括不同的用户对该文件的权限

![](https://img-blog.csdn.net/20180831080642330?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```
drwxr-xr-x.  3 root root     97 8月  24 23:04 abrt      
-rw-r--r--.  1 root root     16 8月  24 23:08 adjtime
```


我们拿 abrt 这个目录和 adjtime这个文件来解释。

 前11个字符确定不同用户能对文件干什么  
 第一个字符代表文件（-）、目录（d），链接（l） 设备（C） 块设备（b）  
 其余字符每3个一组（rwx），读（r）、写（w）、执行（x）

*    第一组rwx：文件所有者的权限是读、写和执行
*    第二组rw-：与文件所在组同一组的用户的权限是读、写但不能执行
*    第三组r--：  不与文件所有者同组的其他用户的权限是读不能写和执行

 最后的 **.**  代表没有ACL扩展权限  + 代表有ACL扩展权限。                                           
针对文件：

*    r 表示可以读取文件
*    w 表示可以对文件内容做修改
*    x 表示文件可执行

针对目录：

*    r 表示可以列出目录内容(可以使用ls)，前提是得有 x 权限，因为如果你都不能进入这个目录，你怎么列出该目录的内容
*    w 表示可以在目录中增删改查，前提是得有 x 权限，因为如果你都不能进入这个目录，你怎么增删改查
*    x 表示可以进入目录，但不一定能读取目录内的内容。要想读取目录的内容，需要有 r 权限，要想修改，需要有 w 权限！

注：目录的权限比较特殊，可以看到，x 权限是目录最基本的权限，因为如果你都不能进入该目录，那读取内容和增删改查就更无从所起了。还有一个就是当你对目录拥有 rwx 权限的时候。倘若目录中有一个文件，该文件对你的权限是没有 w 权限的，但是因为你先匹配到该目录的权限，可以对目录中的文件修改，所以你可以强制修改该文件，保存退出！

![](https://img-blog.csdn.net/20180831224818137?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

后面的各个字段的含义：

*   3 代表文件的链接数是3；
*   root 代表该文件的所属用户；
*   root 代表该文件所属的组。一般文件所属的组就是文件所属主所在的组，也有特殊情况，可以自己随意修改！！
*   97 代表文件的大小；
*   8月 24 23:04代表文件最后的修改日期；
*   abrt 表示的是文件名

```
例子:         这里有几个用户，其UID和GID分别如下      
root 用户：   uid=0(root)     gid=0(root)     groups=0(root)      
xie  用户：   uid=1000(xie)   gid=1001(john)  groups=1001(john)      
john 用户：   uid=1001(john)  gid=1001(john)  groups=1001(john)      
james用户：   uid=1002(james) gid=1002(james) groups=1002(james)      
jerry用户：   uid=1003(jerry) gid=1002(james) groups=1002(james)       
然后，我们以root身份新建一个文件夹 test ，则test默认的权限如下      
drwxr-xr-x 2 root root 4096 Aug 31 19:16 test      1
我们修改其文件权限，文件所有者，文件所在组，修改为如下      1
drwxrwx--- 2 xie james 4096 Aug 31 19:19 test      1
最后得到的结论如下：       1
xie用户的身份是文件所有者，其可以在该目录下创建文件，且创建文件的信息如下  -rw-r--r-- 1 xie   john  0 Aug 31 19:45 file1      1
john用户的身份是other，对该文件不可以做任何操作      1
james用户是身份是文件所属组的成员，其在该目录下创建文件的信息如下  -rw-rw-r-- 1 james james 0 Aug 31 19:32 file2      1
jerry用户的身份是文件所属组的成员，其在该目录下创建文件的信息如下  -rw-r--r-- 1 jerry james 0 Aug 31 19:43 file3
```


### 文件权限的修改 

修改文件的权限 chmod

```
chmod  755 abrt                         //赋予abrt权限rwxr-xr-x      
chmod  u=rwx ，g=rx，o=rx abrt          //赋予abrt  rwxr-xr-x权限。u=用户权限，g=组权限，o=不同组其他用户权限      
chmod  u-x ， g+w   abrt               //给abc去除用户执行的权限，增加组写的权限      
chmod  a+r          abrt               //给所有用户添加读的权限
```


修改文件的所有者 chown 和 所属组 chgrp

```
chown   bob    adjtime           // 改变 adjtime 的所有者为 bob      
chgrp   root   abrt             //改变 adjtime 所属的组为 root       
chgrp   -R  root  abrt           //改变abc这个目录及其目录下所有的文件的所属的组织为 root      
chown   ‐R  root  abrt           //改变abc这个目录及其下面所有的文件和目录的所有者是 root
```


注：修改文件和文件夹所有者和所属组方法都是一样的，如果要把文件夹内的文件的所有者和所属组都修改了，要加 -R 参数。chown除了可以修改属主属性，还可以修改所属组属性。                        语法： chown    属主:属组   文件

```
chown   john:james   adjtime 　　  //改变 adjtime 的所有者为john，所属组为 james
```


### ACL控制权限 setfacl  、 getfacl

setfacl ：设置文件访问控制规则

*    -m ,    给文件加扩展ACL规则  ， setfacl  -m    u:bob:r   abrt ;  
*    -x  ,    给文件移除扩展ACL规则 , setfacl   -x    u:bob      abrt ;
*    -b ，  移除文件所有的ACL规则 ，setfacl  -b   u:bob   abrt ;
*    -R ,    递归的对所有目录内所有的文件和目录进行操作 ，  setfacl  -R -m  u:bob:rwx   abrt ;
*    -d ,    设置默认的ACL规则

getfacl : 获取文件访问控制规则

```
例子:         这里有几个用户，其UID和GID分别如下      
用户a：       uid=1006(a)  gid=1006(a)  groups=1006(a)      
用户b：       uid=1007(b)  gid=1007(b)  groups=1007(b)      
用户c：       uid=1007(c)  gid=1000(root)  groups=1000(root)       
我们以root身份在根目录下新建一个文件file，其权限如右：-rw-r--r-- 1 root root 4 Aug 31 23:00 file      
其acl信息如下： getfacl file      
            # file:  file      
            # owner: root      1
            # group: root      1
            user::rw-      1
            group::r--      1
            other::r--      1
所以用户a和用户b只对他有读的权限，现在我们要让用户a拥有rwx权限，而用户b不变化      1
设置用户a的ACL权限：setfacl -m u:a:rwx  file      1
设置完acl之后，文件file的权限就变成了： -rw-rwxr--+ 1 root root 4 Aug 31 23:01 file        1
注：用户的组权限位变化，等于acl信息中的mask值，还多了一个+扩展权限位。此时虽然用户c是root组中的，但是用户c对文件file的权限还仍然是之前的r--，而不是rwx      1
查看file的acl信息  getfacl  file   ，显示如下      1
            # file:  file      2
            # owner: root      2
            # group: root      2
            user::rw-      2
            user:a:rwx     // 用户a的ACL权限      2
            group::r--            2
            mask::rwx      // 等于用户之前的权限与acl设置的权限进行 或运算      2
            other::r-      2
可知，用户a对其有rwx权限，用户b对其仍然只有 r 的权限      2
若要移除acl ，则  setfacl  -x  u:a  file                     2
移除了之后，a的权限就又还是只有 r 的权限了
```


注：若a用户原来对文件只有 r 权限，设置的acl是 setfacl  -m  u:a:w  file  ，则设置完acl后，用户a只对文件有 w 权限，没有 r 权限了

 倘若要让一个文件夹内的所有已存在文件都继承于文件夹的设置的acl的权限属性，  可以使用  setfacl  -R  -m  u:a:rwx  文件名

### Umask、Suid、Sgid、粘滞位

**UMSK**

root用户的umask值默认是0022，普通用户的umask值默认是 0002。umask值是可以手动修改的。第一位是特殊位，配置了SUID(4) 、SGID(2) 和 粘滞位(1) 才有值。

所以当root用户去创建一个文件夹的时候，其权限就是 777-022=755，而创建一个文件的时候，其权限就是 755-111=644

![](https://img-blog.csdn.net/20180903192554743?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

普通用户去创建一个文件夹的时候，其权限就是 777-002=775，而创建一个文件的时候，其权限就是 775-111-664

![](https://img-blog.csdn.net/20180903192714458?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

所以root用户和普通用户权限的不同之处就在于group组。root用户的属组位置的 w 只在自己的手中。而普通用户的属组权限只要是和他在一个组内，就拥有 w 权。

**SUID 、SGID和粘滞位**

*   suid的作用：用于执行文件，以文件的拥有者的身份运行该文件。     chmod u+s  文件名
*   sgid的作用:  用于目录，在该目录下建立的所有文件和目录，属组都继承该目录的属组，且该组内其他成员修改目录内的文件时，其属主和属组都不改变！     chmod  g+s  目录
*   粘滞位stick  bit 的作用: 用于目录，在该目录建立的文件或目录，只有建立者可以删除和修改，其他用户无法删除和修改 chmod  o+t  目录

SUID的作用：SUID是set uid的简称，它出现在文件所属主权限的执行位上面，标志为 s 。当设置了SUID后，UMSK第一位为4。我们知道，我们账户的密码文件存放在/etc/shadow中，而/etc/shadow的权限为 ----------。也就是说：只有root用户可以对该目录进行操作，而其他用户连查看的权限都没有。当普通用户要修改自己的密码的时候，可以使用passwd这个指令。passwd这个指令在/bin/passwd下，当我们执行这个命令后，就可以修改/etc/shadow下的密码了。那么为什么我们可以通过passwd这个指令去修改一个我们没有权限的文件呢？这里就用到了suid，suid的作用是让执行该命令的用户以该命令拥有者即root的权限去执行，意思是当普通用户执行passwd时会拥有root的权限，这样就可以修改/etc/passwd这个文件了。命令：   chmod u+s  文件名

![](https://img-blog.csdn.net/20180831202631662?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

使用suid需要满足的几个条件

*   SUID只对可执行文件有效
*   调用者对该文件有执行权
*   在执行过程中，调用者会暂时获得该文件的所有者权限
*   该权限只在程序执行的过程中有效

SGID的作用：SGID是set gid的简称，它出现在文件所属组权限的执行位上面，标志位 s 。它用当设置了SGID后，UMSK第一位为2 。它作用于目录，当用户 a 对某一目录有rwx 权限时，该用户就可以在该目录下建立文件，新建文件的所属主和所属组继承于 a。当另一个用户 b 也对该目录有rwx权限的时候，就可以修改 a 创建的文件，而且修改后的文件所属主和所属组都会变成 b！但是如果该目录用SGID修饰，则所有拥有 rwx权限的用户在这个目录下建立的文件都是属于这个目录所属的组。当其他用户修改时，有三种情况：

1.  当两个用户都是属于group组内，则修改后的文件的所属主和所属组都不会，而且修改文件时不会提示read only！可以正常修改
2.  当两个用户都是属于other时，则修改后的文件的所属组不变，所属主会变成另一个用户，修改文件时会提示read only，必须强制保存退出
3.  当一个用户是group组，一个是other时，则修改后的文件的所属组不变，所属主会变成另一个用户，修改文件时会提示read only，必须强制保存退出

SGID的例子代码

```
例子:         这里有几个用户，其UID和GID分别如下      
s1 用户：     uid=1004(s1) gid=1005(s1) groups=1005(s1),1004(sgid)      
s2 用户:      uid=1005(s2) gid=1006(s2) groups=1006(s2),1004(sgid)       
我们以root身份在根目录下创建一个 test 文件 ，其默认权限如右：drwxr-xr-x 2 root root 4096 Aug 31 20:54 test      
修改其默认权限如右：drwxrwx--- 2 root sgid 4096 Aug 31 20:54 test      
因为s1和s2都是组sgid内的成员，所以s1和s2均可对test文件执行rwx权限      
s1进入test内，创建一个文件 file1 ，其权限如右： -rw-rw-r-- 1 s1 s1 3 Aug 31 21:01 file1      
s2进入test内，用vim打开file1文件，显示文件read only。对其修改，强制保存退出！然后file1的权限如右：-rw-rw-r-- 1 s2  s2 3 Aug 31 21:04 file1      1
可见，s2可以强制修改s1创建的文件。虽然s2对于file1来说是other，但是因为s1和s2均属于sgid组，所以可以对test文件夹内的数据拥有读写执行的权限，所以可以用vim对其强制修改保存退出。修改后，file1文件的所属用户和所属组都变成了s2的了。      1
显然，这不是我们想要的，如果我们想要s2修改了文件后，文件的所有用户和所属组都不变的话，我们就需要用到SGID了。      1
执行  chmod  g+s  /test       1
s1再新建一个文件file2，其权限如右：-rw-rw-r-- 1 s1  sgid 3 Aug 31 21:03 file2    可见，其所属组继承了文件夹test的所属组      1
s2再用vim打开file2，不提示文件read only了，修改，保存退出！然后file2的权限如右：-rw-rw-r-- 1 s1  sgid 13 Aug 31 21:04 file2      1
可见，执行了SGID后，同一组内的其他成员修改文件后，其属主和属组都不发生变化！
```


粘滞位的作用：

 SBIT即Sticky Bit，它出现在其他用户权限的执行位上，标志位为 t，当设置了粘滞位后，UMSK第一位为1 。它只能用来修饰一个目录。当某一个目录拥有SBIT权限时，则任何一个能够在这个目录下建立文件的用户，该用户在这个目录下所建立的文件，只有该用户自己和root可以修改和删除，其他用户均不可以修改和删除！！我们知道/tmp是系统的临时文件目录，所有的用户在该目录下拥有所有的权限，也就是说在该目录下可以任意创建、修改、删除文件，那如果用户A在该目录下创建了一个文件，用户B将该文件删除或修改了，这种情况我们是不能允许的。为了达到该目的，就出现了stick  bit(粘滞位)的概念。

无论是两个用户都属于group组，还是都属于other，或者是一个属于group，一个属于other，都不能对其他人创建的文件进行修改和删除！！

```
例子:         这里有几个用户，其UID和GID分别如下      
用户a：       uid=1006(a)  gid=1007(a)  groups=1007(a)      
用户b：       uid=1007(b)  gid=1008(b)  groups=1008(b)       
我们以root身份在根目录下创建一个 test 文件 ，其默认权限如右：drwxr-xr-x 2 root root 4096 Aug 31 20:54 test      
我们修改其权限：drwxrwxrwx 2 root root 4096 Aug 31 20:54 test      
用户a匹配test文件夹的other权限，拥有rwx权限，进入test内，新建文件夹file1，其权限如右：-rw-rw-r-- 1 a a 5 Aug 31 21:45 file1      
用户b匹配test文件夹的other权限，拥有rwx权限，进入test内，可以执行删除file1。因为用户b拥有文件夹test的rwx权限，所以可以对其文件夹内的所有数据删除。即使file1的other权限是只读！！      
很明显，这不是我们想要的      1
执行  chmod  o+t /test      1
s1再新建一个文件file2，其权限如右：-rw-rw-r-- 1 a  a 3 Aug 31 21:03 file2          1
s2再尝试删除file2，删不了！！只有文件的主人可以删除      1
可见，执行了栈滞位后，只有文件的所有者才可以删除文件
```


注意：有时你设置了s 或 t  权限，你会发现它变成了S或T，这是因为在那个位置上你没有给它x(可执行)的权限，这样的话这样的设置是不会有效的，你可以先给它赋上x的权限，然后再给s或t  的权限

相关文章：[Linux用户方面的加固](https://blog.csdn.net/qq_36119192/article/details/82906799#Linux%E7%94%A8%E6%88%B7%E6%96%B9%E9%9D%A2%E7%9A%84%E5%8A%A0%E5%9B%BA)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10917 人正在系统学习中