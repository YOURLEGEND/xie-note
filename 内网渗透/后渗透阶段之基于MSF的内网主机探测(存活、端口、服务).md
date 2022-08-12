**目录**



[主机存活探测](#t0 "主机存活探测")



[端口扫描](#t1 "端口扫描 ") 



[服务探测](#t2 "服务探测 ") 



* * *



 ![](https://img-blog.csdnimg.cn/20191215141315915.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



当我们通过代理可以进入某[内网](https://so.csdn.net/so/search?q=%E5%86%85%E7%BD%91&spm=1001.2101.3001.7020)，需要对内网主机的服务进行探测。我们就可以使用MSF里面的内网主机探测模块了。



在这之前，先修改 /etc/proxychains.conf ，加入我们的代理。



然后 proxychains msfconsole 启动MSF，这样，我们的MSF就可以探测内网主机了。 



或者是在MSF里面添加0.0.0.0/24的路由，这样，只要被控主机可以到达的地址，我们也就可以探测！



**MSF的search支持type搜索：**



```
search scanner type:auxiliary
```




![](https://img-blog.csdnimg.cn/20190617084240128.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



主要介绍以下几种：



### 主机存活探测



```
auxiliary/scanner/discovery/udp_sweep    #基于udp协议发现内网存活主机      
auxiliary/scanner/discovery/udp_probe    #基于udp协议发现内网存活主机      
auxiliary/scanner/netbios/nbname         #基于netbios协议发现内网存活主机      
auxiliary/scanner/portscan/tcp           #基于tcp进行端口扫描(1-10000)，如果开放了端口，则说明该主机存活
```




![](https://img-blog.csdnimg.cn/20191217235745299.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



![](https://img-blog.csdnimg.cn/20191217235834324.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



### 端口扫描 



```
auxiliary/scanner/portscan/tcp           #基于tcp进行端口扫描(1-10000)      
auxiliary/scanner/portscan/ack           #基于tcp的ack回复进行端口扫描，默认扫描1-10000端口
```




![](https://img-blog.csdnimg.cn/20191218000326905.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



### 服务探测 



```
auxiliary/scanner/ftp/ftp_version        #发现内网ftp服务，基于默认21端口      
auxiliary/scanner/ssh/ssh_version        #发现内网ssh服务，基于默认22端口      
auxiliary/scanner/telnet/telnet_version  #发现内网telnet服务，基于默认23端口      
auxiliary/scanner/dns/dns_amp            #发现dns服务，基于默认53端口      
auxiliary/scanner/http/http_version      #发现内网http服务，基于默认80端口      
auxiliary/scanner/http/title             #探测内网http服务的标题      
auxiliary/scanner/smb/smb_version        #发现内网smb服务，基于默认的445端口         
use auxiliary/scanner/mssql/mssql_schemadump  #发现内网SQLServer服务,基于默认的1433端口      
use auxiliary/scanner/oracle/oracle_hashdump  #发现内网oracle服务,基于默认的1521端口       1
auxiliary/scanner/mysql/mysql_version    #发现内网mysql服务，基于默认3306端口      1
auxiliary/scanner/rdp/rdp_scanner        #发现内网RDP服务，基于默认3389端口      1
auxiliary/scanner/redis/redis_server     #发现内网Redis服务，基于默认6379端口      1
auxiliary/scanner/db2/db2_version        #探测内网的db2服务，基于默认的50000端口      1
auxiliary/scanner/netbios/nbname         #探测内网主机的netbios名字
```




**示例：** 



![](https://img-blog.csdnimg.cn/201906170903165.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)



 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")



![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)



 参考文章：[基于 MSF 发现内网存活主机](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650455220&idx=3&sn=11ecb9a2cde71a1ccda79e00f338be5f&chksm=83bbd950b4cc5046b68f64fa95a7cba3e0a3274b8000e7035a36a590f518b3156d4f44f9687e&scene=0&xtrack=1&key=2e27ce8b4f8ddf52a67778e4f2f186b6718516055123fdf0a24b2a6fb420b9f08a07702e5ee74576bd3a9c0c4445147aa6afe9c6eb70811ef169ab33a62d1ac636230637923388311c6455b6e88dd7ff&ascene=14&uin=MjIwMDQzNjQxOQ%3D%3D&devicetype=Windows+10&version=62060833&lang=zh_CN&pass_ticket=7AHGAKnFaa7HlzEmyWkvvzTKdugWYYAOfirvJKla4AWf105Ry1ROnnNOhqZNwC5t "基于 MSF 发现内网存活主机")