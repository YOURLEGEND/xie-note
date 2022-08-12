关于Redhat系列中网络启动失败的解决办法

**报错： Failed to start LSB: Bring up/down networking.**

            **RTNETLINK answers: File exists**

![](https://img-blog.csdn.net/20180929121053488?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

方法一：

*   错误原因：和 NetworkManager 服务有冲突
*   解决办法：关闭 NetworkManger 服务， systemct  stop  NetworkManager，并且禁止开机启动 systemctl  disable  NetworkManager

方法二：(Centos系列)

*   错误原因：/etc/udev/rules.d/70-persistent-net.rules 文件里的MAC地址和 /etc/sysconfig/network-scripts/ifcfg-eth0的MAC地址不一致
*   解决办法：修改 /etc/udev/rules.d/70-persistent-net.rules 文件的MAC地址，保持和 ifcfg-eth0 中的MAC地址一致

注：因为Redhat中没有 /etc/udev/rules.d/70-persistent-net.rules 这个文件

方法三： (Redhat系列)

*   解决办法：也还是MAC地址不一致
*   解决办法： ip  addr  查看 MAC地址， 然后把 /etc/sysconfig/network-scripts/ifcfg-enoxxxx 里的MAC地址改成一致

![](https://img-blog.csdn.net/20180929121654706?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20180929121551109?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

方法三脚本实现

```
#!/bin/bash      
# Author: 谢公子      
# Date: 2018-10-10      
# Function: 对于启动网卡失败，出现 RTNETLINK answers：File exists 的解决脚本      
NAME=`ifconfig | awk -F: 'NR==1{print $1}'`      
MAC=`ifconfig |awk 'NR==4{print $2}'`      
path="/etc/sysconfig/network-scripts/ifcfg-$NAME"      
sed -i '/^HWADDR/d' $path      
sed -i '$a HWADDR='$MAC'' $path      1
systemctl start network      1
status=`systemctl status network | awk 'NR==3{print $2}'`      1
if [ $status == 'active' ];then      1
 echo "修复成功，网卡已经启动"      1
else      1
 echo "修复失败！！"      1
fi
```


### 相关文章：[Linux中的网络配置(nmcli、Team)](https://blog.csdn.net/qq_36119192/article/details/82597630)