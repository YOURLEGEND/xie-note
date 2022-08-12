以下方法Windows和Linux均适用！

将python文件编译成exe文件需要[第三方库](https://so.csdn.net/so/search?q=%E7%AC%AC%E4%B8%89%E6%96%B9%E5%BA%93&spm=1001.2101.3001.7020)：pyinstaller ，执行命令：pip3 install pyinstaller 即可安装。

```
#指定国内源安装      
pip3 install pyinstaller -i http://pypi.douban.com/simple/ --trusted-host pypi.douban.com
```


​​​​​​​

![](https://img-blog.csdnimg.cn/20200908111154875.png)

进入python文件路径下(不能含有中文)，执行命令：pyinstaller -F 1.py 

指定python3版本：python3  -m pyinstaller  1.py![](https://img-blog.csdnimg.cn/20200902224221388.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

执行完命令后，会生成如下文件。在dist目录下会有生成的exe文件，该exe文件就是我们要生成的文件。

![](https://img-blog.csdnimg.cn/20200902224247973.png)

![](https://img-blog.csdnimg.cn/20200902224326839.png)

![](https://img-blog.csdnimg.cn/20200902224359356.png)

加图标编译

```
pyinstaller --icon 360.ico --onefile 1.py       
生成的文件双击不弹出黑框      
pyinstaller --noconsole --onefile  1.py
```


![](https://img-blog.csdnimg.cn/20200908112033693.png)

**Pyinstaller参数如下** 

<table border="0"><tbody><tr><td>-F,&nbsp;–onefile</td><td>产生一个文件用于部署&nbsp;(参见XXXXX).</td></tr><tr><td>-D,&nbsp;–onedir</td><td>产生一个目录用于部署&nbsp;(默认)</td></tr><tr><td>-K,&nbsp;–tk</td><td>在部署时包含&nbsp;TCL/TK</td></tr><tr><td>-a,&nbsp;–ascii</td><td>不包含编码.在支持Unicode的python版本上默认包含所有的编码.</td></tr><tr><td>-d,&nbsp;–debug</td><td>产生debug版本的可执行文件</td></tr><tr><td>-w,–windowed,–noconsole</td><td>使用<a title="Windows" one-link-mark="yes">Windows</a>子系统执行.当程序启动的时候不会打开命令行(只对Windows有效)</td></tr><tr><td>-c,–nowindowed,–console</td><td>使用控制台子系统执行(默认)(只对Windows有效)</td></tr><tr><td>-s,–strip</td><td>可执行文件和共享库将run&nbsp;through&nbsp;strip.注意Cygwin的strip往往使普通的win32&nbsp;Dll无法使用.</td></tr><tr><td>-X,&nbsp;–upx</td><td>如果有UPX安装(执行Configure.py时检测),会压缩执行文件(Windows系统中的DLL也会)(参见note)</td></tr><tr><td>-o&nbsp;DIR,&nbsp;–out=DIR</td><td>指定spec文件的生成目录,如果没有指定,而且当前目录是PyInstaller的根目录,会自动创建一个用于输出(spec和生成的可执行文件)的目录.如果没有指定,而当前目录不是PyInstaller的根目录,则会输出到当前的目录下.</td></tr><tr><td>-p&nbsp;DIR,&nbsp;–path=DIR</td><td>设置导入路径(和使用PYTHONPATH效果相似).可以用路径分割符(Windows使用分号,Linux使用冒号)分割,指定多个目录.也可以使用多个-p参数来设置多个导入路径</td></tr><tr><td>--icon=&lt;FILE.ICO&gt;</td><td>将file.ico添加为可执行文件的资源(只对Windows系统有效)</td></tr><tr><td>–icon=&lt;FILE.EXE,N&gt;</td><td>将file.exe的第n个图标添加为可执行文件的资源(只对Windows系统有效)</td></tr><tr><td>-v&nbsp;FILE,&nbsp;–version=FILE</td><td>将verfile作为可执行文件的版本资源(只对Windows系统有效)</td></tr><tr><td>-n&nbsp;NAME,&nbsp;–name=NAME</td><td>可选的项目(产生的spec的)名字.如果省略,第一个脚本的主文件名将作为spec的名字</td></tr></tbody></table>