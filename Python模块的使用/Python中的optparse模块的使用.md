optparse模块主要用来为脚本传递命令参数，采用预先定义好的选项来解析[命令行](https://so.csdn.net/so/search?q=%E5%91%BD%E4%BB%A4%E8%A1%8C&spm=1001.2101.3001.7020)参数。

实例化一个 OptionParser 对象(可以带参，也可以不带参数)，带参的话会把参数变量的内容作为帮助信息输出。

示例：

```
import optparse      
usage="python %prog -H <target host> -p/-P <target ports>"  #用于显示帮助信息      
parser=optparse.OptionParser(usage)  #创建对象实例      
parser.add_option('-H',dest='Host',type='string',help='target host')   ##需要的命令行参数      
parser.add_option('-P','-p',dest='Ports',type='string',help='target ports' default="20,21")  ## -p/-P 都可以      
(options,args)=parser.parse_args()      
print(options.Host)      
print(options.Ports)
```


各个参数的含义：

*   dest：用于保存输入的临时变量，其值通过options的属性进行访问，存储的内容是dest之前输入的参数，多个参数用逗号分隔
*   type: 用于检查命令行参数传入的参数的数据类型是否符合要求，有 string，int，float 等类型
*   help：用于生成帮助信息
*   default: 给dest的默认值，如果用户没有在命令行参数给dest分配值，则使用默认值

![](https://img-blog.csdnimg.cn/20181103100719103.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)