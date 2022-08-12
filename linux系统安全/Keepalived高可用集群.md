**目录**

[Keepalived](#t0)

[Keepalived实现主备网站](#t1)

* * *

### Keepalived

**Keepalived**软件起初是专为LVS负载均衡软件设计的，用来管理并监控LVS集群系统中各个服务器节点的状态，后来又加入了实现高可用的VRRP功能。因此 Keepalived 除了能够管理LVS软件外，还可以作为其他服务（例如：NGINX、Haproxy、MySQL等）的高可用解决方案。 Keepalived软件主要是通过 VRRP 协议实现高可用功能的

Keepalived 服务的三个重要功能：

*   管理LVS负载均衡软件
*   实现LVS集群节点的健康检查中
*   作为系统网络服务的高可用性（failover）

配置文件： /etc/keepalived/keepalived.conf

完整的 keepalived 配置文件由3部分组成：

*   全局定义部分     global\_defs vrrp
*   实例定义部分     vrrp\_instance VI\_1
*   虚拟服务器定义部分     virtual\_server

### Keepalived实现主备网站

![](https://img-blog.csdn.net/20181012202405285?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

某学校计划搭建一个校内论坛网站，为了保证并发量的需求，决定使用两台NGINX服务器提供WEB服务，通过Keepalived实现两台WEB服务器的负载均衡和高可用。

我们这里只配置keepalived部分的配置

**Server1(Master)**

*   安装keepalived： yum  -y install  keepalived
*   修改配置文件 /etc/keepalived/keepalived.conf
*   启动keepalived：systemctl start keepalived

```
global_defs {      
   notification_email {      
     acassen@firewall.loc      
     failover@firewall.loc      
     sysadmin@firewall.loc      
   }      
   notification_email_from Alexandre.Cassen@firewall.loc      
   smtp_server 192.168.200.1      
   smtp_connect_timeout 30      1
   router_id LVS_DEVEL      1
}       1
vrrp_instance VI_1 {      1
    state MASTER   ######      1
    interface eno16777736  #######      1
    virtual_router_id 51    #########master=backup      1
    priority 150    #########master>backup      1
    advert_int 1      1
    authentication {      2
        auth_type PASS      2
        auth_pass 1111      2
    }      2
    virtual_ipaddress {             ##对外的虚拟ip      2
        192.168.10.100   #############master=backup      2
    }      2
}
```


**Server2(Backup)：**

*   安装keepalived： yum  -y install  keepalived
*   修改配置文件 /etc/keepalived/keepalived.conf
*   启动keepalived：systemctl start keepalived

```
global_defs {      
   notification_email {      
     acassen@firewall.loc      
     failover@firewall.loc      
     sysadmin@firewall.loc      
   }      
   notification_email_from Alexandre.Cassen@firewall.loc      
   smtp_server 192.168.200.1      
   smtp_connect_timeout 30      1
   router_id LVS_DEVEL      1
}       1
vrrp_instance VI_1 {      1
    state MASTER   ######      1
    interface eno16777736  #######      1
    virtual_router_id 51    #########master=backup      1
    priority 100    #########master>backup      1
    advert_int 1      1
    authentication {      2
        auth_type PASS      2
        auth_pass 1111      2
    }      2
    virtual_ipaddress {            ##对外的虚拟ip      2
        192.168.10.100   #############master=backup      2
    }      2
}
```
