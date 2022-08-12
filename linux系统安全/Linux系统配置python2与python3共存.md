**目录**

[安装python3相关的依赖库](#t0)

[下载安装python3.6.2](#t1)

* * *

        在Linux系统中，由于 [yum](https://so.csdn.net/so/search?q=yum&spm=1001.2101.3001.7020) 使用的是python2，所以安装了python2.x，相应的也安装了pip工具。但是在很多时候，我们需要使用到python3环境。所以，我们就得想办法在Linux机器上同时安装python2和python3环境了。

我这里使用的是[Centos7](https://so.csdn.net/so/search?q=Centos7&spm=1001.2101.3001.7020)系统，默认安装了Python2.7.5的环境。

![](https://img-blog.csdnimg.cn/20200203202102437.png)

### 安装python3相关的依赖库

```
yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc make -y
```


![](https://img-blog.csdnimg.cn/20200203203452970.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 下载安装python3.6.2

```
wget https://www.python.org/ftp/python/3.6.2/Python-3.6.2.tar.xz      
tar -xJvf  Python-3.6.2.tar.xz      
cd Python-3.6.2      
./configure prefix=/usr/local/python3      
make && make install      
安装完毕，/usr/local/目录下就会有python3了       
我们可以添加python3软链到执行目录下/usr/bin      
ln -s /usr/local/python3/bin/python3 /usr/bin/python3       1
然后添加pip3的软连接      1
ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3
```


![](https://img-blog.csdnimg.cn/20200203212108925.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200203212359599.png)

### 

关于如何卸载python3：[linux如何删除Python3\_黄菜鸟的博客-CSDN博客\_linux删除python3](https://blog.csdn.net/sinat_36154268/article/details/109593979 "linux如何删除Python3_黄菜鸟的博客-CSDN博客_linux删除python3")