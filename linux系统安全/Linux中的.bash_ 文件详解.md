**目录**

[.bash\_history](#t0)

[.bash\_logout](#t1)

[.bash\_profile](#t2)

[.bashrc](#t3)

* * *

每个用户的[根目录](https://so.csdn.net/so/search?q=%E6%A0%B9%E7%9B%AE%E5%BD%95&spm=1001.2101.3001.7020)下都有四个这样的 bash文件,他们是隐藏文件,需要使用-a参数才会显示出来

![](https://img-blog.csdnimg.cn/20190714140432532.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### .bash\_history

该文件保存了当前用户输入过的历史命令

**history命令：**读取历史命令文件中的目录到历史命令缓冲区和将历史命令缓冲区中的目录写入命令文件。

历史命令是被保存在内存中的，当退出或者登录shell时，会自动保存或读取。在内存中，历史命令仅能够存储1000条历史命令，该数量是由 /etc/profile 中的 **`HISTSIZE`** `变量`进行控制的**。**默认是不显示命令的执行时间，命令的执行时间history 已经记录，只是没有显示。

如想查询某个用户在系统上执行了什么命令，可以使用 root 用户身份登录系统，检查Home目录下的用户主目录下的“**.**bash\_history**”**文件，该文件记录了用户所使用的命令和历史信息。

history命令显示的是 /~/.bash\_history文件中的命令和当前shell下输入的缓存中的命令。

也就是说，/~/.bash\_history文件记录的命令是上一次登录及其之前的命令。我们这次登录执行的命令并不会立即写入/~/.bash\_history文件中，而是存在缓存中。只有我们注销后，该缓存中的命令才会写入 /~/.bash\_history 文件中。

*   history 10 。显现最近10条命令
*   history -a：将当前终端缓存中的命令写入 /~/.bash\_history 中(追加)
*   history -w：将当前终端缓存中的命令写入 /~/.bash\_history 中(覆盖)
*   history -c：清除当前终端缓存中的所有命令
*   history -r : 将/~/.bash\_history 文件中的命令读入到缓存中

当在终端执行以下命令时，该终端就不会记录历史命令

```
export HISTFILE=/dev/null export HISTSIZE=0
```


### .bash\_logout

该文件的用途是用户注销时执行的命令，默认是没有内容的

![](https://img-blog.csdnimg.cn/20190714140759839.png)

如果我们往该文件中写入 history -c ，则在每次注销时，会清除缓存中的命令。

![](https://img-blog.csdnimg.cn/20190714140914475.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### .bash\_profile

此文件为系统的每个用户设置环境信息，当用户第一次登录时，该文件被执行。并从 /etc/profile.d 目录的配置文件中搜集shell的设置。

我们也可以往该文件中增加新内容，环境变量啥的

![](https://img-blog.csdnimg.cn/20190714141502345.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### .bashrc

此文件为每一个运行bash shell的用户执行此文件。当bash shell被打开时，该文件被读取。我们也可以往该文件中增加新内容，可以定义别名和函数

![](https://img-blog.csdnimg.cn/20190714141901565.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

每次修改 .bashrc 后，使用 source  ~/.bashrc（或者 . ~/.bashrc）就可以立刻加载修改后的设置，使之生效。

一般会在 .bash\_profile 文件中显式调用.bashrc。登陆linux启动bash时首先会去读取~/.bash\_profile文件，这样~/.bashrc也就得到执行了，你的个性化设置也就生效了。

关于环境变量的读取顺序:  
用户登录 ->> 加载~/.bash\_profile --> bash\_profile中配置了首先是使~/.bashrc生效  
 

参考文章：[Linux| 用户目录下三个bash文件的作用(.bash\_history,.bash\_logout,.bash\_profile,.bashrc)](https://blog.csdn.net/u011479200/article/details/86501366)

                  [/etc/profile /etc/bashrc ~/.bash\_profile ~/.bashrc ~/.bash\_logout 说明及区别](/etc/profile%20/etc/bashrc%20~/.bash_profile%20~/.bashrc%20~/.bash_logout%20%E8%AF%B4%E6%98%8E%E5%8F%8A%E5%8C%BA%E5%88%AB)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)10949 人正在系统学习中