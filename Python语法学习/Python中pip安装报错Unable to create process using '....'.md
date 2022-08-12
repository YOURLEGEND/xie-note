因为我本人在电脑上安装了python2和python3，所以在安装的时候，把两个python的安装目录都安装到G盘了。然后两个python的执行文件分别改成了 python2.exe 和  python3.exe  。这才是导致出现错误的具体原因。

因为我改了文件名，而pip-script.py文件还是去找的 python.exe 文件。所以，自然是找不到啦。

![](https://img-blog.csdn.net/20181007154144828?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

此错误的解决办法很简单，就是把可执行文件改回 python.exe 就可以了。

还有一个问题就是，既然安装了 python2 和 python3 ，那么使用 pip的时候到底是用的2的pip还是3的pip呢。这个跟你的环境变量有关。

如果你的环境变量3的版本在前面的话，就使用3的pip。如果是2在前面的话，就是使用2的pip。

刚才我的是 2 的环境变量在上面，所以默认是使用 2 的pip。我现在把3的环境变量移到了上面，所以，以后是默认使用3的pip。

![](https://img-blog.csdn.net/20181007154653467?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![](https://img-blog.csdn.net/20181007154730884?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)