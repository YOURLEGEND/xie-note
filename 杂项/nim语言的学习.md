**目录**

[nim](#t0)

[nim的优点](#t1)

[安装nim编译器和nimble包管理器](#t2)

[nim文件的编译](#t3) 

* * *

nim
---

    Nim是一个新型的静态类型、命令式编程语言，支持过程式、函数式、[面向对象](https://so.csdn.net/so/search?q=%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1&spm=1001.2101.3001.7020)和泛型编程风格而保持简单和高效。Nim从Lisp继承来的一个特殊特性--抽象语法树(AST)作为语言规范的一部分，可以用作创建领域特定语言的强大宏系统。Nim是一个编译型的具有垃圾收集的系统编程语言，有着极其卓越的生产/性能比。Nim的设计集中在三个E上：即效率(efficiency)、表达能力(expressiveness)和优雅(elegance)。而我们学习nim的目的就是为了做免杀。大多数杀软对于新型的偏门小类型语言的免杀效果并不好。github上一个比较出名的利用nim进行免杀的项目：[https://github.com/byt3bl33d3r/OffensiveNim](https://github.com/byt3bl33d3r/OffensiveNim "https://github.com/byt3bl33d3r/OffensiveNim")

### nim的优点

*   可以直接编译为C、C++、Objective-C和Javascript。
*   语法简单，不依赖运行时虚拟机。
*   具有极其成熟的外部接口API。
*   跨平台交叉编译。
*   可以将代码直接编译为Javascript，甚至初步支持WebAssembly

### 安装nim编译器和nimble包管理器

```
apt update      
apt install nim  #安装nim语言
```


如果要编译出Windows下可以运行的exe和dll文件，那么必须安装mingw

```
apt install mingw-w64
```


然后使用包管理器nimble安装winim包。nimble就和python中的pip一样，用了安装依赖的库包

```
nimble install winim
```


### nim文件的编译 

nim语言编写的文件后缀为 .nim ，以下为编译方法

```
一般的编译参数:       
c：编译成C语言。例如：nim c test.nim      
cpp：编译成C++语言。例如：nim cpp test.nim      
objc：编译成objc语言      
js：编译成javascript脚本, 可以建一个html文件在<script src="test.js"></script>里运行.      
-d:release：进行release编译。nim cc -d:release test.nim      
-r：编译完成后运行程序      
--cincludes：包含 当前目录(./)的 c头文件.      1
--cpu：指定架构，如：nim cc -cpu amd64 ,  nim cc -cpu:arm。--cpu参数有：i386, m68k, alpha, powerpc, powerpc64, powerpc64el, sparc, vm, hppa, ia64, amd64, mips, mipsel, arm, arm64, js, nimvm, avr, msp430, sparc64, mips64, mips64el, riscv64, esp, wasm32       1
编译64位的      1
nim c -d:mingw --app:gui --cpu:amd64 -d:danger -d:strip --opt:size --passc=-flto --passl=-flto test.nim       1
编译x86的      1
nim c -d:mingw --app:gui --cpu:i386 -d:danger -d:strip --opt:size --passc=-flto --passl=-flto test.nim
```


未完待续。。