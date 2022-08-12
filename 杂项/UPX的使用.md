UPX
---

     UPX (the Ultimate Packer for eXecutables)是一款先进的可执行程序文件压缩器，压缩过的[可执行文件](https://so.csdn.net/so/search?q=%E5%8F%AF%E6%89%A7%E8%A1%8C%E6%96%87%E4%BB%B6&spm=1001.2101.3001.7020)体积缩小50%-70% ，这样减少了磁盘占用空间、网络上传下载的时间和其它分布以及存储费用。 通过 UPX 压缩过的程序和程序库完全没有功能损失和压缩之前一样可正常地运行，对于支持的大多数格式没有运行时间或内存的不利后果。 UPX 支持许多不同的可执行文件格式 包含 Windows 95/98/ME/NT/2000/XP/CE 程序和动态链接库、DOS 程序、 Linux 可执行文件和核心。

### 使用UPX压缩程序

```
以下命令分别对1.exe进行压缩，压缩程度越来越高，压缩时间相应的也越来越长      
upx.exe -o 2.exe 1.exe      
upx.exe --best  -o 3.exe 1.exe      
upx.exe --brute -o 4.exe 1.exe
```


可以看到，文件大小越来越小了 

![](https://img-blog.csdnimg.cn/20201211201940505.png)

未完待续。。