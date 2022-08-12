**目录**

[安装python3.X环境](#t0)

[安装部署开启django](#t1)

* * *

由于Linux系统默认自带的是2.X环境，所以我们需要去安装3.X环境的python。

### 安装python3.X环境

1、使用下面的命令下载Python-3.6.3.tar.xz的安装包，对应版本可以去 https://www.python.org/ftp/python/ 对应起来：

```
wget https://www.python.org/ftp/python/3.6.3/Python-3.6.3.tar.xz
```


2、接着解压这个压缩包，命令如下：

```
tar xvf Python-3.6.3.tar.xz
```


3、接着编译安装，进入解压后的目录，执行底下的一个shell脚本configure进行检查，接着编译安装，编译安装过程有点慢，命令如下：

```
cd Python-3.6.3      
./configure      
make && make install
```


5、之后输入命令python3，发现安装成功。

### 安装部署开启django

```
pip3 install Django==2.0.3
```


然后就可以使用命令创建项目了。

进入python3的安装目录，在该目录下会有[django](https://so.csdn.net/so/search?q=django&spm=1001.2101.3001.7020)\-admin.py文件，我们创建test的项目

```
python3 django-admin startproject test
```


然后我们进行test项目目录下，创建 App应用。

```
python3 manage.py startapp App
```


**启动Django ：**

如果我们的服务器是公网地址，那么我们启动Django的时候，地址配置为0.0.0.0

```
python3 manage.py runserver 0.0.0.0:80       
后台运行Django：      
nohup python3 manage.py runserver 0.0.0.0:80 &
```


其中还需要改一个settings.py文件，不然会报Invalid HTTP\_HOST header:错误，把ALLOWED\_HOSTS改为下面，就允许所有主机方式连接：

```
ALLOWED_HOSTS = ['*']
```


文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-137)[Web应用开发](https://edu.csdn.net/skill/python/python-3-137) [Django](https://edu.csdn.net/skill/python/python-3-137)84853 人正在系统学习中