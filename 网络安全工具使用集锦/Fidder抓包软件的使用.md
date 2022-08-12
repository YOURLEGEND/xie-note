**Fiddler**是一款强大的Web调试工具，它能记录客户端和服务端所有的HTTP和HTTPS请求。[Fiddler](https://so.csdn.net/so/search?q=Fiddler&spm=1001.2101.3001.7020)是通过改写HTTP代理，让数据从它那通过，来监控并且截取到数据。Fiddler很屌，在打开它的那一瞬间，它就已经设置好了浏览器的代理了。当你关闭的时候，它又帮你把代理还原了，Fidder默认代理端口是8888。

![](https://img-blog.csdn.net/20181005104959922?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**Request部分详解**

> 1.  Headers —— 显示客户端发送到服务器的 HTTP 请求的 header，显示为一个分级视图，包含了 Web 客户端信息、Cookie、传输状态等。
> 2.  Textview —— 显示 POST 请求的 body 部分为文本。
> 3.  WebForms —— 显示请求的 GET 参数 和 POST body 内容。
> 4.  HexView —— 用十六进制数据显示请求。
> 5.  Auth —— 显示响应 header 中的 Proxy-Authorization(代理身份验证) 和 Authorization(授权) 信息.
> 6.  Raw —— 将整个请求显示为纯文本。
> 7.  JSON - 显示JSON格式文件。
> 8.  XML —— 如果请求的 body 是 XML 格式，就是用分级的 XML 树来显示它。

**Responser部分详解**

> 1.  Transformer —— 显示响应的编码信息。
> 2.  Headers —— 用分级视图显示响应的 header。
> 3.  TextView —— 使用文本显示相应的 body。
> 4.  ImageVies —— 如果请求是图片资源，显示响应的图片。
> 5.  HexView —— 用十六进制数据显示响应。
> 6.  WebView —— 响应在 Web 浏览器中的预览效果。
> 7.  Auth —— 显示响应 header 中的 Proxy-Authorization(代理身份验证) 和 Authorization(授权) 信息。
> 8.  Caching —— 显示此请求的缓存信息。
> 9.  Privacy —— 显示此请求的私密 (P3P) 信息。
> 10.  Raw —— 将整个响应显示为纯文本。
> 11.  JSON - 显示JSON格式文件。
> 12.  XML —— 如果响应的 body 是 XML 格式，就是用分级的 XML 树来显示它 。

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)