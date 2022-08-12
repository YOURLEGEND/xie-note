**目录**

[Docker容器和KVM虚拟化](#t0 "Docker容器和KVM虚拟化")

[Docker的安装和使用](#t1 "Docker的安装和使用")

[基于Docker的漏洞复现环境Vulhub的使用](#t2 "基于Docker的漏洞复现环境Vulhub的使用 ") 

* * *

Docker容器和KVM虚拟化
---------------

      [Docker](https://so.csdn.net/so/search?q=Docker&spm=1001.2101.3001.7020) 容器是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的Linux机器上，也可以实现虚拟化。Docker容器是一种轻量级、可移植、自包可以在含的软件打包技术，使应用程序几乎任何地方以相同的方式运行。开发人员在自己笔记本上创建并测试好的容器，无需任何修改就能够在生产系统的虚拟机、物理服务器或公有云主机上运行。容器是完全使用沙箱机制，相互之间不会有任何接口，几乎没有性能开销，可以很容易地在机器和数据中心中运行。最重要的是，他们不依赖于任何语言、框架包括系统。简单的说，容器就是在隔离环境运行的一个进程，如果进程停止，容器就会销毁。隔离的环境拥有自己的系统文件，IP地址，主机名等。

**Docker技术介绍**：Docker是通过内核[虚拟化](https://so.csdn.net/so/search?q=%E8%99%9A%E6%8B%9F%E5%8C%96&spm=1001.2101.3001.7020)技术（namespaces及cgroups cpu、内存、磁盘io等）来提供容器的资源隔离与安全保障等。由于Docker通过操作系统层的虚拟化实现隔离，所以Docker容器在运行时，不需要类似虚拟机（VM）额外的操作系统开销，提高资源利用率。

**Linux容器技术，容器虚拟化和kvm虚拟化的区别：**

*   容器：共用宿主机内核，运行服务，损耗少，启动快，性能高
*   容器虚拟化：不需要硬件的支持。不需要模拟硬件，共用宿主机的内核，启动时间秒级(没有开机启动流程)
*   kvm虚拟化： 需要硬件的支持，需要模拟硬件，可以运行不同的操作系统，启动时间分钟级(开机启动流程)

**Docker和KVM虚拟化的优点**

*   Docker解决了软件和操作系统环境之间的依赖，能够让独立服务或应用程序在不同的环境中，得到相同的运行结果。docker镜像有自己的文件系统。
*   Kvm解决了硬件和操作系统之间的依赖，Kvm独立的虚拟磁盘，xml配置文件。

Docker的安装和使用
------------

**docker的安装**

```
curl -s https://get.docker.com/ | sh   #一键安装Docker，root权限运行。
```


![](https://img-blog.csdnimg.cn/20191212115346112.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**查看Docker版本** 

```
docker version
```


 **docker服务的启动与停止**

```
systemctl start docker              #启动      
systemctl stop docker               #关闭docker       
systemctl restart  docker           #重启docker服务       
systemctl daemon-reload             #守护进程重启
```


**docker镜像的管理**  

```
docker images         #查看本地镜像      
docker images -a      #查看所有的镜像      
docker images php     #查看仓库名为php的镜像      
docker rmi -f 镜像ID     #强制删除镜像         
docker rmi -f 镜像名A:tag 镜像名B:tag    #删除多个镜像      
docker rmi -f $(docker images -aq)      #删除全部镜像      
docker save          #导出镜像      例如：docker image save centos > docker-centos7.4.tar.gz        
docker load          #导入镜像       例如：docker image load -i docker-centos7.4.tar.gz        
docker search xx     #查找相关镜像   例如：docker search redis       1
docker search -s 30 redis     #查找start大于30的redis镜像      1
docker pull  name:标签    #从查找的镜像中下载下来,标签默认是latest  例如：docker pull  redis 等价于 docker pull redis:latest
```


![](https://img-blog.csdnimg.cn/20191210225201390.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20191210232220597.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70) **docker容器的启动、停止、查看和删除**

```
docker run  -d -P --name xxx REPOSITORY:TAG                  #根据镜像启动容器      
    -d:让容器在后台运行      
    -P:将容器内部使用的网络端口映射到我们使用的主机上      
    -p:自定义端口映射，如 -p 8002:80 ，意思就是将容器的80端口映射到宿主机的8002端口      
    --network=host:直接把宿主机的网络映射给容器      
    --name:该参数可选，指定容器的名字      
docker ps                                   #查看运行中的容器      
docker ps –a                                #查看所有的容器      
docker start    容器ID                      #启动容器      1
docker stop     容器ID                      #停止容器      1
docker restart  容器ID                      #重启容器      1
docker rm   容器ID                          #删除容器，删除容器前需停止该容器      1
docker rm  `docker ps -aq`                  #删除全部容器
```


![](https://img-blog.csdnimg.cn/20191210232605285.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20191210230225943.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

当启动容器后， 这里会有一个端口映射，此时我们访问宿主机的 9001端口就行了

![](https://img-blog.csdnimg.cn/20191213110156465.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20191212112532353.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  **进入docker容器进行管理** 

```
docker exec -it  容器id或容器名字 /bin/bash
```


![](https://img-blog.csdnimg.cn/20191210232941906.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**docker容器中文件的上传和下载**

```
上传本地/root/test.txt 文件到容器ID为ff2bc5d3945b的docker的/root/目录下      
docker cp /root/test.txt  ff2bc5d3945b:/root/       
下载容器ID为ff2bc5d3945b的docker的/root/test2.txt文件到本地的/root/目录下·      
docker cp ff2bc5d3945b:/root/test2.txt /root/
```


![](https://img-blog.csdnimg.cn/20200317174621455.png)

**导入导出容器**

```
docker export 容器ID > /opt/test.tar      #导出当前容器镜像到/opt/test.tar      
docker import /opt/test.tar              #导入/opt/test.tar到容器
```


**查看 WEB 应用程序日志**

```
docker logs -f 容器ID      #可以查看容器内部的标准输出。
```


**查看容器的进程** 

```
docker top 容器名
```


**配置docker镜像加速**

docker默认是从国外search和pull，所以这里我们需要添加一个国内的镜像地址

```
#打开该文件      
vim /etc/docker/daemon.json         
#然后加入下面这些内容,阿里云的镜像      
{      
 "registry-mirrors":["https://6kx4zyno.mirror.aliyuncs.com"]      
}
```


基于Docker的漏洞复现环境Vulhub的使用 
-------------------------

vulhub的地址：[Vulhub - Docker-Compose file for vulnerability environment](https://vulhub.org "Vulhub - Docker-Compose file for vulnerability environment")

Vulhub是一个基于 `docker` 和 `docker-compose` 的漏洞环境集合，进入对应目录并执行一条语句即可启动一个全新的漏洞环境。

关于如何安装 Docker 和 Docker-compose 就不再赘述。直接启动对应靶机的容器。

启动Docker：systemctl  start  docker

进入对应的靶机目录，这里我选择weblogic的CVE-2017-10271漏洞，直接一键启动：docker-compose up -d

![](https://img-blog.csdnimg.cn/20190817155418233.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在漏洞复现完成后，还是在漏洞的目录下移除环境，命令：docker-compose  down

![](https://img-blog.csdnimg.cn/20190817155921133.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[Docker容器的安装与使用](https://www.cnblogs.com/gongcheng-/p/10283508.html "Docker容器的安装与使用") 

                  [Docker教程|菜鸟教程](https://www.runoob.com/docker/docker-tutorial.html "Docker教程|菜鸟教程")

文章知识点与官方知识档案匹配，可进一步学习相关知识

[云原生入门技能树](https://edu.csdn.net/skill/cloud_native/cloud_native-3eb56d157f784765b43f6f2ef0f28aac)[容器(docker)](https://edu.csdn.net/skill/cloud_native/cloud_native-3eb56d157f784765b43f6f2ef0f28aac)[安装docker](https://edu.csdn.net/skill/cloud_native/cloud_native-3eb56d157f784765b43f6f2ef0f28aac)3253 人正在系统学习中