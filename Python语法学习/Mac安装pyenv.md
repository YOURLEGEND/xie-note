**目录**

[Pyenv](#t0)

[安装python版本](#t1)

[python版本切换](#t2)

* * *

Pyenv
-----

pyenv它是一个简单的Python版本管理工具。前身为Pythonbrew，pyenv允许你改变全局的python版本，安装多种不同的python版本，设置应用指定的python版本以及创建/管理虚拟的python环境（”virtualenv’s”）。所有这些都在\*NIX的机器上完成（Linux和OS X），它工作在用户空间，因而不需要sudo命令。

mac上安装pyenv，可以使用brew。关于brew的安装，传送门：[安装Homebrew](https://xie1997.blog.csdn.net/article/details/113479429)

```
brew install pyenv       
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc      
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc      
echo 'eval "$(pyenv init -)"' >> ~/.bashrc
```


![](https://img-blog.csdnimg.cn/20210131182531908.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210131182716346.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 安装python版本

使用以下命令进行python版本的安装与卸载，安装的版本会在 `~/.pyenv/versions`目录下

```
pyenv install 2.7.18      #安装python2.7.18版本      
pyenv uninstall 2.7.18    #卸载python2.7.18版本       
# 查看已经安装的Python版本      
pyenv versions       
# 查看可安装的版本      
pyenv install -l
```


![](https://img-blog.csdnimg.cn/20210131183306910.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210131183615729.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### python版本切换

pyenv 的 global、local、shell 的优先级关系是：shell > local > global

```
# global 全局设置 一般不建议改变全局设置      
pyenv global <python版本>       
# shell 会话设置 只影响当前的shell会话      
pyenv shell <python版本>       
# 取消 shell 会话的设置      
pyenv shell --unset       1
# local 本地设置 只影响所在文件夹      1
pyenv local <python版本>       1
# 查看当前的 Python 版本      1
pyenv version
```


![](https://img-blog.csdnimg.cn/2021013118490510.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)