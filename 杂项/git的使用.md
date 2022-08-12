**目录**

[创建本地git仓库](#t0)

[提交文件到本地仓库](#t1)

[版本回退](#t2)

[文件删除](#t3)

[Git密钥生成及Github设置](#t4)

[向Github仓库提交数据](#t5)

[从Github库克隆数据](#t6)

* * *

### **创建本地git仓库**

在windows下使用git命令行模式。

```
mkdir  test          #创建文件夹      
cd     test          #进入该文件夹      
git    init          #将该文件夹变成可以通过git管理的仓库，这时，在test文件夹下会创建一个.git的隐藏目录，这个目录是Git来跟踪管理版本库的，没事千万不要手动修改这个目录里面的文件，不然改乱了，就把Git仓库给破坏了。
```


然后我们的代码项目就必须在test文件夹内。我们在test文件夹下创建一个文件1.txt，内容为如下：

![](https://img-blog.csdnimg.cn/20190422192005477.png)

### **提交文件到本地仓库**

那么，我们如何将1.txt文件提交到仓库呢？ 

```
git add .      #将当前文件夹内的所有文件添加到Git仓库，如果只想添加一个文件，则git add 1.txt      
git commit -m "注释"  #告诉Git，将文件提交到仓库，-m后面输入的是本次提交的说明，可以输入任意内容，当然最好是有意义的，这样你就能从历史记录里方便地找到改动记录。
```


git commit成功之后，会有提示，多少个file changed改动，多少个insertions插入等等。如下

![](https://img-blog.csdnimg.cn/2019042219191631.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

为什么Git添加文件需要`add`，`commit`一共两步呢？因为`commit`可以一次提交很多文件，所以你可以多次`add`不同的文件，比如：

```
git add file1.txt      
git add file2.txt file3.txt      
git commit -m "add 3 files."
```


第一步是用`git add`把文件添加进去，实际上就是把文件修改添加到暂存区；

第二步是用`git commit`提交更改，实际上就是把暂存区的所有内容提交到当前分支。

因为我们创建Git版本库时，Git自动为我们创建了唯一一个`master`分支。所以，现在，`git commit`就是往`master`分支上提交更改。你可以简单理解为，需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。

### **版本回退**

git   log ，该命令显示从最近到最远的提交日志，如下图可以看到3次历史记录。

![](https://img-blog.csdnimg.cn/20190426183036308.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

那么我们怎么回退呢？首先，Git必须知道当前版本是哪个版本，在Git中，用`HEAD`表示当前版本，也就是最新的提交cbfdd0.....，上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个`^`比较容易数不过来，所以写成`HEAD~100`。

我们现在回退到上上一个版本，也就是第一个版本！

```
git reset --hard HEAD^^
```


![](https://img-blog.csdnimg.cn/20190426183615610.png)

那如果我现在发现回退错了，又想回到刚刚的版本怎么操作？只要我们刚刚的那个窗口没关闭，我们找到刚刚的ID值，就可以回退过去了。

```
git reset --hard cbfdd0cb415b8a4c72161289f8ce110958438c2a
```


![](https://img-blog.csdnimg.cn/20190426183951744.png)

### **文件删除**

将如我们要将仓库的文件删除，那该怎么办？我们先可以直接在文件夹里面将文件删除，然后 **git status** 查看状态

这里状态显示我们删除了一个文件

![](https://img-blog.csdnimg.cn/20190426185852499.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

但是仓库里并没有删除，我们还需要在仓库里删除

```
git  rm  1.txt      
git  commit -m "删除了文件"
```


![](https://img-blog.csdnimg.cn/20190426190035364.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### Git密钥生成及Github设置

本地 Git 仓库 和 github 仓库要通过SSH加密才能传输，所以需要让github认证本地的SSH Key。

认证之前，则先使用Git生成SSH Key。

进入如下目录：C:\\Users\\用户名\\.ssh  ，在该目录下使用命令创建公私钥

```
ssh-keygen -t rsa -C "xx@xx.com"  #这里的邮箱是你的github登录邮箱
```


![](https://img-blog.csdnimg.cn/20200109113542678.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后会在C:\\Users\\用户名\\.ssh 目录下生成公钥文件 id\_rsa.pub  和 私钥文件 id\_rsa

![](https://img-blog.csdnimg.cn/20200109113758463.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后我们进入github的settings里，点击 SSH and GPG keys ，然后 New SSH key，将id\_rsa.pub里的内容复制进去即可。

![](https://img-blog.csdnimg.cn/20200109113936860.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200109114500395.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

验证是否配置成功

```
ssh -T git@github.com
```


![](https://img-blog.csdnimg.cn/20200109114252616.png)

### 向Github仓库提交数据

```
进入某个仓库下面： git remote add origin git@github.com:xie1997/test
```


添加后，远程库的名字就是`origin`，这是Git默认的叫法，也可以改成别的，但是`origin`这个名字一看就知道是远程库。

当Github上的仓库是空的，我们第一次将本地文件提交到Github上的话，我们需要使用 -u 参数，加上了`-u`参数，Git不但会把本地的`master`分支内容推送的远程新的`master`分支，还会把本地的`master`分支和远程的`master`分支关联起来

```
git push -u origin master
```


如果不是第一次操作的话，我们就不用 -u 参数了！

```
git push  origin master
```


![](https://img-blog.csdnimg.cn/2019042619245147.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
git pull --rebase origin   将远程仓库和本地仓库同步
```


### 从Github库克隆数据

```
克隆Github的test仓库到本地： git clone git@github.com:xie1997/test
```


![](https://img-blog.csdnimg.cn/20190426192819118.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-62c30f9c31f64a1d96af732c47c93f04)[Git入门](https://edu.csdn.net/skill/gml/gml-62c30f9c31f64a1d96af732c47c93f04)[Git简介](https://edu.csdn.net/skill/gml/gml-62c30f9c31f64a1d96af732c47c93f04)10973 人正在系统学习中