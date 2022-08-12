安装Hexo并初始化启动
------------

首先安装[node](https://so.csdn.net/so/search?q=node&spm=1001.2101.3001.7020).js和Git

```
brew install node.js      
brew install git       
#查看安装的版本      
node -v      
git --version
```


![](https://img-blog.csdnimg.cn/20210309200441314.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210309200520478.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20210309200602978.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后安装[hexo](https://so.csdn.net/so/search?q=hexo&spm=1001.2101.3001.7020)

```
npm install -g hexo-cli
```


![](https://img-blog.csdnimg.cn/20210309201205758.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

创建博客目录，初始化

```
mkdir blog      
cd blog      
hexo init
```


![](https://img-blog.csdnimg.cn/20210309201707202.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

启动

```
hexo s
```


![](https://img-blog.csdnimg.cn/20210309201918402.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2021030920190520.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 安装主题

切换到themes目录下，使用如下命令下载主题文件

```
git clone https://github.com/blinkfox/hexo-theme-matery.git
```


### 修改主题

修改blog下的 \_config.yml 主题配置文件

相关文章：[https://github.com/blinkfox/hexo-theme-matery/blob/develop/README\_CN.md](https://github.com/blinkfox/hexo-theme-matery/blob/develop/README_CN.md)