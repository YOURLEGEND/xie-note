在很多时候，机器不能出网，但是又需要安装依赖包。这时，可以从网上下载依赖包然后手动进行安装，如下：

```
下载单个离线包      
pip download -d test 包名      
批量下载离线包      
pip download -d test -r requirements.txt
```


![](https://img-blog.csdnimg.cn/20210228210023337.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210228210040169.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

下载完之后，然后手动安装

```
安装单个离线包：      
pip install --no-index 包名      
批量安装离线包      
pip install --no-index --find-links=/目录名/ -r requirements.txt
```


![](https://img-blog.csdnimg.cn/20210228210204343.png)