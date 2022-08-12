**目录**

[对未经过安全认证的RPM包进行安全检查](#t0)

[Linux用户方面的加固](#t1)

[设定密码策略](#t2)[​](#t2)

[对用户密码强度的设定](#t3)

[对用户的登录次数进行限制](#t4)

[禁止ROOT用户远程登录](#t5)

[设置历史命令保存条数和账户超时时间](#t6)

[设置只有指定用户组才能使用su命令切换到root用户](#t7)

[对Linux账户进行管理](#t8)

[对重要的文件进行锁定，即使ROOT用户也无法删除](#t9)

[建立日志服务器](#t10)

* * *

对未经过安全认证的RPM包进行安全检查
-------------------

rpm  -qp   xxx.rpm   --scripts     查看rpm包中的脚本信息

Linux用户方面的加固
------------

### 设定密码策略

修改  /etc/login.defs 配置文件

*   PASS\_MAX\_DAYS      90               密码最长有效期
*   PASS\_MIN\_DAYS       10               密码修改之间最小的天数
*   PASS\_MIN\_LEN          8                密码长度
*   PASS\_WARN\_AGE     7                 口令失效前多少天开始通知用户修改密码

### ![](https://img-blog.csdn.net/20181011120928325?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

脚本实现设定密码策略

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


![](https://img-blog.csdn.net/20181013091030245?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 对用户密码强度的设定

打开 **/etc/pam.d/sysetm-auth** 文件 ，修改如下。我们设置新密码不能和旧密码相同，同时新密码至少8位，还要同时包含大字母、小写字母和数字

```
password    requisite     pam_pwquality.so try_first_pass local_users_only retry=3 authtok_type=  difok=1 minlen=8 ucredit=-1 lcredit=-1 dcredit=-1
```


![](https://img-blog.csdn.net/20181011152029674?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

*   difok= ：此选项用来定义新密码中必须要有几个字符和旧密码不同
*   minlen=：此选项用来设置新密码的最小长度
*   ucredit= ：此选项用来设定新密码中可以包含的大写字母的最大数目。-1 至少一个
*   lcredit=：此选项用来设定新密码中可以包含的小写字母的最大数目
*   dcredit=：此选项用来设定新密码中可以包含的数字的最大数目 

**注**：这个密码强度的设定只对普通用户有限制作用，root用户无论修改自己的密码还是修改普通用户的时候，不符合强度设置依然可以设置成功

![](https://img-blog.csdn.net/20181012145755110?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

脚本实现对用户密码强度的设定

```
#！ /bin/bash      
# Author:谢公子      
# Date: 2018-10-12      
# Function: 对用户密码强度的设定，新密码不能和旧密码相同，同时新密码至少8位，还要同时包含大字母、小写字母和数字      
sed -i '/pam_pwquality.so/c\password    requisite     pam_pwquality.so try_first_pass local_users_only retry=3 authtok_type=  difok=1 minlen=8 ucredit=-1 lcredit=-1 dcredit=-1' /etc/pam.d/system-auth
```


### 对用户的登录次数进行限制

有一些攻击性的软件是专门采用暴力破解密码的形式反复进行登录尝试，对于这种情况，我们可以调整用户登录次数限制，使其密码输入3次后自动锁定，并且设置锁定时间，在锁定时间内即使密码输入正确也无法登录

打开 **/etc/pam.d/sshd**  文件，在 #%PAM-1.0 的下面，加入下面的内容，表示当密码输入错误达到3次，就锁定用户150秒，如果root用户输入密码错误达到3次，锁定300秒。锁定的意思是即使密码正确了也登录不了

```
auth required pam_tally2.so deny=3 unlock_time=150 even_deny_root root_unlock_time300
```


![](https://img-blog.csdn.net/20181012152512872?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

当输入3次密码错误时

![](https://img-blog.csdn.net/20181012153808667?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

pam\_tally2   查看被锁定的用户

pam\_tally2  --reset  -u  username      将被锁定的用户解锁

![](https://img-blog.csdn.net/20181012151101230?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

脚本设置对用户的登录次数做限制

```
#！ /bin/bash      
# Author:谢公子      
# Date: 2018-10-12      
# Function: 对用户登录输入错误密码次数做限制      
n=`cat /etc/pam.d/sshd | grep "auth required pam_tally2.so "|wc -l`      
if [ $n -eq 0 ];then      
sed -i '/%PAM-1.0/a\auth required pam_tally2.so deny=3 unlock_time=150 even_deny_root root_unlock_time300' /etc/pam.d/sshd      
fi
```


### 禁止ROOT用户远程登录

禁止ROOT用户[远程登录](https://so.csdn.net/so/search?q=%E8%BF%9C%E7%A8%8B%E7%99%BB%E5%BD%95&spm=1001.2101.3001.7020) 。打开  **/etc/ssh/sshd\_config**  

![](https://img-blog.csdn.net/20180930142543421?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

脚本设置禁止ROOT用户远程登录

```
#！ /bin/bash      
# Author:谢公子      
# Date: 2018-10-12      
# Function: 禁止ROOT用户远程登录      
sed -i '/PermitRootLogin/c\PermitRootLogin no'  /etc/ssh/sshd_config
```


### 设置历史命令保存条数和账户超时时间

设置账户保存历史命令条数，超时时间  。打开  /etc/profile ,修改如下

![](https://img-blog.csdn.net/20180930142327734?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

使用脚本修改配置文件

```
#！ /bin/bash      
# Author:谢公子      
# Date: 2018-10-12      
# Function: 修改配置文件，设置历史命令保存条数和账户自动注销时间      
read -p "设置历史命令保存条数：" E      
read -p "设置账户自动注销时间：" F      
sed -i '/^HISTSIZE/c\HISTSIZE='$E'' /etc/profile      
sed -i '/^HISTSIZE/a\TMOUT='$F'' /etc/profile
```


### 设置只有指定用户组才能使用su命令切换到root用户

在linux中，有一个默认的管理组 wheel。在实际生产环境中，即使我们有系统管理员root的权限，也不推荐用root用户登录。一般情况下用普通用户登录就可以了，在需要root权限执行一些操作时，再su登录成为root用户。但是，任何人只要知道了root的密码，就都可以通过su命令来登录为root用户，这无疑为系统带来了安全隐患。所以，将普通用户加入到wheel组，被加入的这个普通用户就成了管理员组内的用户。然后设置只有wheel组内的成员可以使用su命令切换到root用户。

比如，我们将普通用户xie加入wheel组。   usermod  -G  wheel  xie 

然后，我们修改配置文件 **/etc/pam.d/su**  ，将这行的注释给去掉

![](https://img-blog.csdn.net/20181012164845600?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后去 **/etc/login.defs** 末尾加入   **SU\_WHEEL\_ONLY yes**  即可。

![](https://img-blog.csdn.net/20181012171003993?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

当 tom 用户使用su命令切换到root用户时，会提示拒绝权限

![](https://img-blog.csdn.net/20181012193352334?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

使用脚本设置配置文件

```
#！ /bin/bash      
# Author:谢公子      
# Date: 2018-10-12      
# Function: 修改配置文件，使得只有wheel组的用户可以使用 su 权限      
sed -i '/pam_wheel.so use_uid/c\auth            required        pam_wheel.so use_uid ' /etc/pam.d/su      
n=`cat /etc/login.defs | grep SU_WHEEL_ONLY | wc -l`      
if [ $n -eq 0 ];then      
echo SU_WHEEL_ONLY yes >> /etc/login.defs      
fi
```


### 对Linux账户进行管理

*   使用命令 userdel  -r 用户名  删除不必要的账号
*   使用命令 passwd -l  用户名  锁定不必要的账号
*   使用命令 awk -F: '($7=="/bin/bash"){print $1}' /etc/passwd   查看具有登录权限的用户
*   使用命令 awk -F: '($3==0)' /etc/passwd    查看UID为0的账号，UID为0的用户会自动切换到root用户，所以危害很大
*   使用命令 awk -F: '($2=="")' /etc/shadow   查看空口令账号，如果存在空口令用户的话必须设置密码 

使用脚本对账户进行管理

```
#! /bin/bash      
# Author:谢公子      
# Date:2018-10-11      
# Function: 对系统中的用户做检查，加固系统      
echo "系统中有登录权限的用户有："      
awk -F: '($7=="/bin/bash"){print $1}' /etc/passwd      
echo "********************************************"      
echo "系统中UID=0的用户有："      
awk -F: '($3=="0"){print $1}' /etc/passwd      1
echo "********************************************"      1
N=`awk -F: '($2==""){print $1}' /etc/shadow|wc -l`      1
echo "系统中空密码用户有：$N"      1
if [ $N -eq 0 ];then      1
 echo "恭喜你，系统中无空密码用户！！"      1
 echo "********************************************"      1
else      1
 i=1      1
 while [ $N -gt 0 ]      1
 do      2
    None=`awk -F: '($2==""){print $1}' /etc/shadow|awk 'NR=='$i'{print}'`      2
    echo "------------------------"      2
    echo $None      2
    echo "必须为空用户设置密码！！"      2
    passwd $None      2
    let N--      2
 done      2
 M=`awk -F: '($2==""){print $1}' /etc/shadow|wc -l`      2
 if [ $M -eq 0 ];then      2
  echo "恭喜，系统中已经没有空密码用户了！"      3
 else      3
echo "系统中还存在空密码用户：$M"      3
 fi      3
fi
```


### 对重要的文件进行锁定，即使ROOT用户也无法删除

chattr    改变文件或目录的扩展属性

lsattr    查看文件目录的扩展属性

chattr  +i  /etc/passwd /etc/shadow                 //增加属性  
chattr  -i  /etc/passwd /etc/shadow                 //移除属性    
lsattr  /etc/passwd /etc/shadow

![](https://img-blog.csdn.net/20181008121039107?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**使用脚本对重要文件进行锁定**

```
#! /bin/bash      
# Author: 谢公子      
# Date：2018-10-10      
# Function: 锁定创建用户和组的文件，使之无法对用户和组进行操作！      
read -p "警告：此脚本运行后将无法添加删除用户和组！！确定输入Y，取消输入N；Y/N：" i      
case $i in      
      [Y,y])      
            chattr +i /etc/passwd      
            chattr +i /etc/shadow      1
            chattr +i /etc/group      1
            chattr +i /etc/gshadow      1
            echo "锁定成功！"      1
;;      1
      [N,n])      1
            chattr -i /etc/passwd      1
            chattr -i /etc/shadow      1
            chattr -i /etc/group      1
            chattr -i /etc/gshadow      1
            echo "取消锁定成功！！"      2
;;      2
       *)      2
            echo "请输入Y/y or  N/n"      2
esac
```


一个脚本对上面所有的合并了

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


![](https://img-blog.csdn.net/20181013095653980?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

建立日志服务器
-------

日志服务器的好处在于，每个工作服务器将自己的日志信息发送给日志服务器进行集中管理，即使有人入侵了服务器并将自己的登录信息悄悄删除，但由于日志信息实时与日志服务器同步，保证了日志的完整性。以备工作人员根据日志服务器信息对服务器安全进行评测。

![](https://img-blog.csdn.net/20181001104319544?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**客户端的配置：**

打开 /etc/rsyslog.conf 配置文件

你想把哪种类型的日志文件发送给服务端，你就把他原来的对应的目录改成： @192.168.10.20

![](https://img-blog.csdn.net/20181001104539981?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

如果你想吧所有的日志文件都发送给服务器的话，你可以在文件最后加上： \*.\*  @@192.168.10.20:514

![](https://img-blog.csdn.net/2018100110465286?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后重启rsyslog服务： systemctl  restart  rsyslog

**服务器端的配置：**

打开 /etc/rsyslog.conf 配置文件，将这里的注释给去掉

![](https://img-blog.csdn.net/20181001104815518?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后重启rsyslog服务：systemctl  restart  rsyslog

开启防火墙： firewall-cmd  --add-port=514/tcp

这样客户端的相应的日志文件就会保存在服务器端的相应的文件中。

![](https://img-blog.csdn.net/20181001110329978?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中