**目录**

[Selenium的介绍、配置和调用](#t0)

[Selenium的配置](#t1)

[Selenium的调用](#t2)

[Selenium的使用](#t3)

[定位](#t4)

[定位元素的使用](#t5)

[定位下拉标签元素](#t6) 

[在iframe框架之间切换](#t7) 

[上传文件](#t8)

[Webdriver模块的使用](#t9)

[控制浏览器操作的一些方法](#t10) 

[鼠标事件](#t11)

[键盘事件](#t12)

[获取断言信息](#t13)

* * *

Selenium的介绍、配置和调用
=================

**Selenium(浏览器自动化测试框架)** 是一个用于Web应用程序测试的工具。[Selenium](https://so.csdn.net/so/search?q=Selenium&spm=1001.2101.3001.7020)测试直接运行在浏览器中，就像真正的用户在操作一样。支持的浏览器包括IE（7, 8, 9, 10, 11），Firefox，Safari，Google Chrome，Opera等。这个工具的主要功能包括：测试浏览器的兼容性——测试你的应用程序看是否能够很好得工作在不同浏览器和操作系统之上。测试系统功能——创建回归测试检验软件功能和用户需求。支持自动录制动作和自动生成 .Net、Java、Perl等不同语言的测试脚本。

*   框架底层使用JavaScript模拟真实用户对浏览器进行操作。测试脚本执行时，浏览器自动按照脚本代码做出点击，输入，打开，验证等操作，就像真实用户所做的一样，从终端用户的角度测试应用程序。
*   使浏览器兼容性测试自动化成为可能，尽管在不同的浏览器上依然有细微的差别。
*   使用简单，可使用Java，Python等多种语言编写用例脚本。

**Selenium的配置**
---------------

1、安装 Selenium模块：  pip  install  Selenium

2、下载浏览器驱动，Selenium3.x调用浏览器必须有一个[webdriver](https://so.csdn.net/so/search?q=webdriver&spm=1001.2101.3001.7020)驱动文件

*   Chrome驱动文件下载：[点击下载chromedrive](https://chromedriver.storage.googleapis.com/index.html?path=2.35/)
*   Firefox 驱动文件下载 ：[点解下载geckodriver](https://github.com/mozilla/geckodriver/releases)

下载之后，解压到任意目录(路径不要有中文)。

**Selenium的调用**
---------------

```
from selenium import webdriver       
## 如果是chrome浏览器的驱动      
driver=webdriver.Chrome("G:\Anaconda3-5.3.0\chromedriver.exe")       
##如果是firefox浏览器的驱动      
driver=webdriver.Firefox(executable_path="G:\Anaconda3-5.3.0\geckodriver.exe")       
######如果浏览器驱动的目录加入了环境变量的话       1
## 如果是chrome浏览器的驱动      1
driver=webdriver.Chrome()       1
##如果是firefox浏览器的驱动      1
driver=webdriver.Firefox()
```


Selenium的使用
===========

定位
--

**Selenium提供了8种定位方式**

1.  id
2.  name
3.  class  name
4.  tag name
5.  link text
6.  partial link text
7.  xpath
8.  css selector

### **定位元素的使用**

| 定位一个元素 | 定位多个元素 | 含义 |
| --- | --- | --- |
| find\_element\_by\_id | find\_elements\_by\_id | 通过元素id定位 |
| find\_element\_by\_name | find\_elements\_by\_name | 通过元素name定位 |
| find\_element\_by\_class\_name | find\_elements\_by\_class\_name | 通过classname进行定位 |
| find\_element\_by\_tag\_name | find\_elements\_by\_tag\_name | 通过标签定位 |
| find\_element\_by\_link\_text | find\_elements\_by\_link\_tex | 通过完整超链接定位 |
| find\_element\_by\_partial\_link\_text | find\_elements\_by\_partial\_link\_text | 通过部分链接定位 |
| find\_elements\_by\_css\_selector | find\_elements\_by\_css\_selector | 通过css选择器进行定位 |
| find\_element\_by\_xpath | find\_elements\_by\_xpath | 通过xpath表达式定位 |

例如：

```
<html>      
  <body>      
      <form id="loginForm">      
         <input name="username" type="text" classname="xie"  id="key"/>      
         <input name="password" type="password" 型号 />      
         <input name="continue" type="submit" value="Login" />      
         <input name="continue" type="button" value="Clear" />      
      </form>      
      <a href="http://www.baidu.com">百度一下</a>      1
  </body>      1
</html>
```


通过id进行定位第一个input框： find\_element\_by\_id("key") 

通过name进行定位第一个input框：find\_element\_by\_name("username")

通过classname进行定位第一个input框：find\_element\_by\_class\_name("xie")

通过标签tag进行定位input框：find\_element\_by\_tag\_name("input")    //这里input太多了，用input标签定位会出错

通过完整超链接定位a标签: find\_element\_by\_link\_text("百度一下")

用xpath进行定位：

```
用绝对路径进行定位,input[1]代表form下面的第一个input标签，从1开始， input=input[1]      
driver.find_elemant_by_xpath("//html/body/form/input[1]")       
用相对路径进行定位，form标签下的第一个input标签，[1]省略了      
driver.find_element_by_xpath("//form/input")       
用相对路径和属性进行定位，form标签下的input标签的name值等于username的标签      
driver.find_element_by_xpath("//form/input[@name='username']")       1
其他的属性值如果太长，也可以采取模糊方法定位      1
例如页面中有这么一个标签          1
<a href="http://www.baidu.com?name=admin&passwd=pass&action=login">百度一下</a>      1
则可以这么使用       1
driver.find_element_by_xpath("//a[contains(@href,'login')]")
```


以上是普通的情况，存在可以定位的属性，当某个元素的各个属性及其组合都不足以定位时，我们可以利用其兄弟节点或者父节点等各种可以定位的元素进行定位，先看看xpath中支持的方法：

     1、child  选取当前节点的所有子元素

     2、parent  选取当前节点的父节点

     3、descendant选取当前节点的所有后代元素（子、孙等）

     4、ancestor  选取当前节点的所有先辈（父、祖父等）

     5、descendant-or-self选取当前节点的所有后代元素（子、孙等）以及当前节点本身

     6、ancestor-or-self  选取当前节点的所有先辈（父、祖父等）以及当前节点本身

     7、preceding-sibling选取当前节点之前的所有同级节点

     8、following-sibling选取当前节点之后的所有同级节点

     9、preceding   选取文档中当前节点的开始标签之前的所有节点

    10、following   选取文档中当前节点的结束标签之后的所有节点

    11、self  选取当前节点

    12、attribute  选取当前节点的所有属性

    13、namespace选取当前节点的所有命名空间节点

![](https://img-blog.csdn.net/20170626143930261?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaHVpc2VxaXV0aWFu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

上图实例，需要点击订单号为17051915200001的发货按钮，这时候不能直接定位到发货按钮，而是要先定位到订单号元素，再定位他的兄弟节点。参照上图，我们首先定位到td标签中包含订单号的td元素，然后选择其之后的同级节点，following-sibling，我们要找的元素在后面的第8个td标签下，因此定位可以写名为下面的格式

```
driver.find_element_by_xpath("//td[contains(text(),’17051915200001’)]/following-sibling::td[8]/a[@class='link']")
```


但是如果页面中有两个相同的定位元素的话，我们这样使用就有可能会报错

比如有下面两个输入框，一个输入用户名的，一个输入密码的，但是 class 都是等于 inputclass 。这时，如果我们使用class\_name 来定位元素的话，就会出现意想不到的错误

```
<input type="text" class="inputclass" name="username">      
<input type="password"   class="inputclass" name="password"><br/>       
#python代码      
driver.find_element_by_class_name("inputclass").send_keys("admin")      
driver.find_element_by_class_name("inputclass").send_keys("password")
```


我们的本意是在username框内输入admin，password框内输入 password 。但是由于两个框的 class 相同，而我们又是使用class\_name进行元素的定位，所以我们所有的操作都会对第一个元素进行。 

![](https://img-blog.csdn.net/20181011084512714?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

还有如果我们定位的元素页面没有的话，也会报错

比如我们使用这条语句用class\_name来进行定位元素，但是当页面没有 class\_name="aa" 的元素的话，就会报错，表示找不到通过 class name 方法找的元素 aa

```
driver.find_element_by_class_name("aa").send_keys("bb")       
NoSuchElementException: no such element: Unable to locate element: {"method":"class name","selector":"aa"}
```


### **定位下拉标签元素** 

![](https://img-blog.csdnimg.cn/20190111171515461.png)

```
import Select      
Select(driver.find_element_by_xpath("//select[@id='9560af43bfc949c4826d329c352e4eb6_class']")).select_by_index(4)   #定位公共互联网环境
```


### **在iframe框架之间切换** 

![](https://img-blog.csdnimg.cn/20190111171756906.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
#切换到指定的iframe框架      
driver.switch_to.frame("mainFrame")  #切换iframe框架      
driver.switch_to.default_content()   #切换到主框架
```


### **上传文件**

上传文件使用的是AutoIt，安装好后，打开AutoIt Window Info，鼠标选中Finder Tool，然后鼠标左键按住拖到文件名框内，得到如图数据

![](https://img-blog.csdnimg.cn/20190111192844773.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后鼠标选中Finder Tool，鼠标左键按住拖到打开按钮，得到如图数据

![](https://img-blog.csdnimg.cn/20190111192912555.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

打开SciTE Script Editor，输入如下内容

![](https://img-blog.csdnimg.cn/20190111191355903.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
WinWait("CLASS:#32770","",5)  #设置5秒用于等待上传窗口的显示      
ControlFocus("打开","","Edit1")  #把输入焦点定位到上传文本框中      
ControlSetText("打开","","Edit1","C:\Users\17250\Desktop\1.txt")  #输入文件路径      
Sleep(5000)              #等待上传时间，单位毫秒      
ControlClick("打开","","Button1");  #点击打开按钮，开始上传
```


保存为1.au3，点击Tools->Go运行。

然后打开Compile Script to .exe ，然后选中1.au3，它会自动将其转换为1.exe。

![](https://img-blog.csdnimg.cn/20190111192123558.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

在python中使用

```
os.system("C:\\Users\\17250\\Desktop\\1.exe")
```


Webdriver模块的使用
--------------

### **控制浏览器操作的一些方法** 

<table align="left" border="1" cellpadding="1" cellspacing="1" style="width:700px;"><tbody><tr><td>方法</td><td>说明</td></tr><tr><td>set_window_size()</td><td>设置浏览器的大小</td></tr><tr><td>back()</td><td>控制浏览器后退</td></tr><tr><td>forward()</td><td>控制浏览器前进</td></tr><tr><td>refresh()</td><td>刷新当前页面</td></tr><tr><td>clear()</td><td>清除文本</td></tr><tr><td>send_keys (value)</td><td>模拟按键输入</td></tr><tr><td>click()</td><td>单击元素</td></tr><tr><td>submit()</td><td>用于提交表单</td></tr><tr><td>get_attribute(name)</td><td>获取元素属性值</td></tr><tr><td>is_displayed()</td><td>设置该元素是否用户可见</td></tr><tr><td>size</td><td>返回元素的尺寸</td></tr><tr><td>text</td><td>获取元素的文本</td></tr></tbody></table>

###  

###  

###  

###  

###  

###  

###  

###  

###  

###  

###  

###  

### 鼠标事件

在 WebDriver 中， 将这些关于鼠标操作的方法封装在 ActionChains 类提供。

| 方法 | 说明 |
| --- | --- |
| ActionChains(driver) | 构造ActionChains对象 |
| context\_click() | 执行鼠标悬停操作 |
| move\_to\_element(above) | 右击 |
| double\_click() | 双击 |
| drag\_and\_drop() | 拖动 |
| move\_to\_element(above) | 执行鼠标悬停操作 |
| context\_click() | 用于模拟鼠标右键操作， 在调用时需要指定元素定位 |
| perform() | 执行所有 ActionChains 中存储的行为，可以理解成是对整个操作的提交动作 |

###   
 

###  

###  

###  

###  

###  

###  

###  

### 键盘事件

Selenium中的Key模块为我们提供了模拟键盘按键的方法，那就是send\_keys()方法。它不仅可以模拟键盘输入，也可以模拟键盘的操作。

常用的键盘操作如下：

| 模拟键盘按键 | 说明 |
| --- | --- |
| send\_keys(Keys.BACK\_SPACE) | 删除键（BackSpace） |
| send\_keys(Keys.SPACE) | 空格键(Space) |
| send\_keys(Keys.TAB) | 制表键(Tab) |
| send\_keys(Keys.ESCAPE) | 回退键（Esc） |
| send\_keys(Keys.ENTER) | 回车键（Enter） |

组合键的使用

| 模拟键盘按键 | 说明 |
| --- | --- |
| send\_keys(Keys.CONTROL,‘a’) | 全选（Ctrl+A） |
| send\_keys(Keys.CONTROL,‘c’) | 复制（Ctrl+C） |
| send\_keys(Keys.CONTROL,‘x’) | 剪切（Ctrl+X） |
| send\_keys(Keys.CONTROL,‘v’) | 粘贴（Ctrl+V） |
| send\_keys(Keys.F1…Fn) | 键盘 F1…Fn |

### 获取断言信息

不管是在做功能测试还是[自动化测试](https://so.csdn.net/so/search?q=%E8%87%AA%E5%8A%A8%E5%8C%96%E6%B5%8B%E8%AF%95&spm=1001.2101.3001.7020)，最后一步需要拿实际结果与预期进行比较。这个比较的称之为断言。通过我们获取title 、URL和text等信息进行断言。

| 属性 | 说明 |
| --- | --- |
| title | 用于获得当前页面的标题 |
| current\_url | 用户获得当前页面的URL |
| text | 获取搜索条目的文本信息 |

参考文章：[https://blog.csdn.net/weixin\_36279318/article/details/79475388](https://blog.csdn.net/weixin_36279318/article/details/79475388)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[Python入门技能树](https://edu.csdn.net/skill/python/python-3-152)[网络爬虫](https://edu.csdn.net/skill/python/python-3-152)[Selenium](https://edu.csdn.net/skill/python/python-3-152)84711 人正在系统学习中