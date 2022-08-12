**目录**

[配置终端走代理](#t0 "配置终端走代理")

[安装Homebrew](#t1 "安装Homebrew")

[美化终端](#t2 "美化终端")

[安装 Oh My Zsh](#t3 "安装 Oh My Zsh")

[vim设置](#t4 "vim设置")

[安装自动补齐插件](#t5 "安装自动补齐插件")

[安装高亮插件](#t6 "安装高亮插件")

[python2和python3共存](#t7 "python2和python3共存")

[安装python3](#t8 "安装python3")

[安装java](#t9 "安装java")

[安装golang](#t10 "安装golang")

[iterm2如何快速连接VPS](#t11 "iterm2如何快速连接VPS ") 

[iterm2安装lrzsz上传和下载文件](#t12 "iterm2安装lrzsz上传和下载文件")

* * *

配置终端走代理
-------

我们这里的代理工具是SSR。首先，开启SSR，如下：

![](https://img-blog.csdnimg.cn/20210131162050893.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这里socks5端口默认是1086

![](https://img-blog.csdnimg.cn/20210131162126688.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在终端执行命令

```
打开终端，直接执行：(执行后，只对当前终端起作用；重启终端后，默认失效；)      
export http_proxy=socks5://127.0.0.1:1086     #配置http 代理访问      
export https_proxy=socks5://127.0.0.1:1086    #配置https 代理访问      
export all_proxy=socks5://127.0.0.1:1086      #配置http和https访问       
unset http_proxy     #取消http 代理访问      
unset https_proxy    #取消https 代理访问      
unset all_proxy      #取消设置所有代理
```


 ![](https://img-blog.csdnimg.cn/20210131162311484.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果想每次打开终端，都想默认走代理的话，可以把设置代理的命令，放到 shell 配置文件中；mac 下的路径为：~/.bash\_profile

安装Homebrew
----------

Homebrew是mac开源的包管理工具。如同centos的yum和ubuntu的apt-get。

执行以下命令之前，需要先配置终端走代理

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```


### ![](https://img-blog.csdnimg.cn/2021013116181818.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

美化终端
----

### 安装 Oh My Zsh

执行以下命令之前，需要先配置终端走代理

```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```


![](https://img-blog.csdnimg.cn/20210131162527565.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

修改主题

```
vim ~/.zshrc
```


![](https://img-blog.csdnimg.cn/20210131163057364.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### vim设置

vim设置高亮语法，显示行号

```
echo 'syntax on' >> ~/.vimrc      
echo 'set nu!' >> ~/.vimrc
```


![](https://img-blog.csdnimg.cn/2021020115474878.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

效果如图

![](https://img-blog.csdnimg.cn/2021020115481661.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 安装自动补齐插件

执行以下命令之前，需要先配置终端走代理

```
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
```


打开配置文件，修改plugins如下

```
vim ~/.zshrc      
source .zshrc
```


![](https://img-blog.csdnimg.cn/20210131163401701.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

自动补全效果如图，灰色的就是补全的

![](https://img-blog.csdnimg.cn/20210131163619892.png)

### 安装高亮插件

执行以下命令之前，需要先配置终端走代理

```
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```


打开配置文件，修改plugins如下

```
vim ~/.zshrc      
source .zshrc
```


![](https://img-blog.csdnimg.cn/20210131163947964.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

高亮插件效果如下，命令会自动高亮显示

![](https://img-blog.csdnimg.cn/20210131164333342.png)

对于没有的命令，就是红色高亮显示

![](https://img-blog.csdnimg.cn/20210131164505822.png)

python2和python3共存
-----------------

mac默认是安装了python2.7的，但是没有pip2，所以需要我们安装pip2

![](https://img-blog.csdnimg.cn/20210131170709131.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py      
python2 get-pip.py       
然后修改环境变量      
vim ~/.bash_profile      
将以下命令粘贴到环境变量文件中      
export PATH=/Users/xxx/Library/Python/2.7/bin:$PATH      
source ~/.bash_profile
```


但是发现有问题，于是我用的pyenv管理python2。传送门：[Mac安装pyenv](https://xie1997.blog.csdn.net/article/details/113482677 "Mac安装pyenv")

![](https://img-blog.csdnimg.cn/2021013118490510.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 安装python3

使用brew安装python3，安装完成后，输入`which python3 查看安装路径`

```
brew install python3
```


![](https://img-blog.csdnimg.cn/20210131172629394.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

通过不同方法安装python的路径是不一样的

| 来源 | python安装路径 |
| --- | --- |
| 系统默认(2.7) | `/System/Library/Frameworks/Python.framework/Versions/2.7` |
| brew安装(2.7/3.x) | `/usr/local/Cellar/python` |
| 官网pkg安装(3.x) | `/Library/Frameworks/Python.framework/Versions/3.x` |

安装完之后，输入python和python2默认是进python2，输入python3进的是python3

![](https://img-blog.csdnimg.cn/20210131170324319.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

安装java
------

直接去官网下载安装包安装：[Java Downloads | Oracle](https://www.oracle.com/technetwork/java/javase/downloads/index.html "Java Downloads | Oracle")

下载了之后双击运行即可安装。然后加入以下环境变量。

```
export JAVA_HOME="/Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home/bin"      
export CLASSPAHT=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar      
export PATH=$JAVA_HOME/bin:$PATH
```


卸载java
------

```
sudo rm -fr /Library/Internet\ Plug-Ins/JavaAppletPlugin.plugin      
sudo rm -fr /Library/PreferencesPanes/JavaControlPanel.prefPane      
sudo rm -fr ~/Library/Application\ Support/Oracle/Java
```


安装golang
--------

```
brew search go  #查看可用的go版本      
brew install go@1.9  #安装go1.9版本
```


![](https://img-blog.csdnimg.cn/20210131220832208.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210131221954912.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后配置go的环境变量

```
#加go的路径到环境变量      
echo 'export PATH="/usr/local/opt/go@1.9/bin:$PATH"' >> ~/.zshrc      
#更新更改      
source ~/.zshrc       
#查看go是否安装成功      
go version       
#查看go的环境      1
go env
```


![](https://img-blog.csdnimg.cn/20210131222926758.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

iterm2如何快速连接VPS 
----------------

首先，在mac的.ssh目录下使用ssh-keygen公私钥生成一对公私钥，私钥(id\_rsa)留在本地然后将公钥传送到VPS的.ssh目录下，并重命名为：authorized\_keys 。传送门：[Linux中的SSH服务](https://xie1997.blog.csdn.net/article/details/82709867 "Linux中的SSH服务")

![](https://img-blog.csdnimg.cn/20210201155545619.png)

然后在mac的.ssh目录下生成一个vpslogin文件，内容如下

```
set user root      
set host VPS的ip      
set port ssh端口      
set timeout 10      
spawn ssh -p $port $user@$host      
interact       
#如果直接使用账户密码的话，如下：      1
set user root      1
set host 192.168.10.1      1
set password root      1
set port 22      1
set timeout 10      1
spawn ssh -p $port $user@$host      1
expect {      1
        "yes/no" {send "yes\r";exp_continue;}      1
         "*password:*" { send "$password\r" }      1
        }      2
interact
```


![](https://img-blog.csdnimg.cn/20210201155632592.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后在iterm2的profiles新建一个profile name，command这里选择Login Shell，发送的命令为：expect  ~/.ssh/vpslogin

![](https://img-blog.csdnimg.cn/20210201155734573.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后以后只要打开profiles的vps即可连接到远程VPS

![](https://img-blog.csdnimg.cn/20210201155843565.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

iterm2安装lrzsz上传和下载文件
--------------------

首先使用brew安装lrzsz

```
brew install lrzsz
```


然后去github下载文件：[https://github.com/aikuyun/iterm2-zmodem](https://github.com/aikuyun/iterm2-zmodem "https://github.com/aikuyun/iterm2-zmodem")

将如下两个文件移动到 /usr/local/bin 目录下，并赋予777权限

```
mv iterm2-recv-zmodem.sh /usr/local/bin      
mv iterm2-send-zmodem.sh /usr/local/bin      
chmod 777 /usr/local/bin/iterm2-*
```


![](https://img-blog.csdnimg.cn/20210202114607962.png)

然后打开iterm2的profiles->default->editProfiles->Advanced中的Tirgger。添加两条trigger，分别设置 Regular expression，Action，Parameters，如下：

```
1.第一条      
        Regular expression: rz waiting to receive.\*\*B0100      
        Action: Run Silent Coprocess      
        Parameters: /usr/local/bin/iterm2-send-zmodem.sh      
        Instant: checked      
2.第二条      
        Regular expression: \*\*B00000000000000      
        Action: Run Silent Coprocess      
        Parameters: /usr/local/bin/iterm2-recv-zmodem.sh      1
        Instant: checked
```


![](https://img-blog.csdnimg.cn/20210202114756390.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

运行效果如图

![](https://img-blog.csdnimg.cn/20210202114851640.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

安装proxychains4
--------------

```
brew search proxychains      
brew install proxychains-ng       
配置文件      
/usr/local/etc/proxychains.conf
```


![](https://img-blog.csdnimg.cn/20210203183615430.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)