**目录**

[安装Bower](#t0)

[Bower的使用](#t1)

* * *

### 安装Bower

Windows下安装Bower之前，先安装好 **nodejs** 和 **msysgit** 环境

然后我们就可以使用npm包管理工具下载并全局安装bower：

```
npm install -g bower
```


全局安装bower 后，可以查看Bower的帮助信息，使用命令：

```
bower help
```


![](https://img-blog.csdnimg.cn/20190222104534122.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70) 

### Bower的使用

我们在项目目录下执行初始化当前工程的bower，此操作会在当前目录下生成 bower.json 文件：

```
bower init
```


![](https://img-blog.csdnimg.cn/20190222104739645.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

使用bower安装某个特定类库，例如 jquery

然后就可以看到项目文件夹下多了bower\_components目录，该目录下又有 jquery的目录

```
bower install jquery                   #安装 jquery ，安装多个库用空格隔开
```


删除包,例如jquery (如果包已经被依赖，则不能删除)

```
bower uninstall jquery
```


使用 bower 更新某个特定类库，例如jquery:

```
bower update jquery
```


![](https://img-blog.csdnimg.cn/20190222105513127.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

bower默认的下载的包目录是 bower\_components

![](https://img-blog.csdnimg.cn/2019022210565918.png)

如果我们想修改默认目录，我们可以加一个 **.bowerrc 文件**。然后在该文件中指定目录。如下，这样安装的所有包就都会在该目录下的package包下面了

```
{      
"directory": "package/"      
}
```


注：使用 bower install jquery **\--save**才会把 jquery 依赖记入到 bower.json。要安装某个版本使用 #，如安装 juqery1.9.1，可以使用

```
bower  install  jquery#1.9.1
```


   除了用包名安装，也可以指定 git 地址进行安装，如

```
bower  install  https://github.com/jquery/jquery
```


参考：[https://www.jianshu.com/p/d9e46b5a8f80](https://www.jianshu.com/p/d9e46b5a8f80)