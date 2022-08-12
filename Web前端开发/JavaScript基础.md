**目录**

[JavaScript](#t0)

[变量、数据类型和对象](#t1)

[变量](#t2)

[JavaScript的数据类型](#t3)

[对象](#t4) 

[BOM浏览器对象模型](#t5)

[window](#t6)

[location](#t7)

[document](#t8)

[screen](#t9)

[history](#t10)

[DOM文档对象模型](#t11)

[HTML DOM 对象 - 方法和属性](#t12)

* * *

JavaScript
==========

**JavaScript**是一种直译式脚本语言，是一种动态类型、弱类型、基于原型的语言，内置支持类型。它的解释器被称为JavaScript引擎，为浏览器的一部分，广泛用于客户端的脚本语言，最早是在[HTML](https://baike.baidu.com/item/HTML)（标准通用标记语言下的一个应用）网页上使用，用来给HTML网页增加动态功能

变量、数据类型和对象
----------

### **变量**

JavaScript中的变量用 var 定义，变量名是大小写英文、数字、`$`和`_`的组合，且不能用数字开头。变量名也不能是JavaScript的关键字，如`if`、`while`等。申明一个变量用`var`语句

```
var a; // 申明了变量a，此时a的值为undefined      
var $b = 1; // 申明了变量$b，同时给$b赋值，此时$b的值为1      
var s_007 = '007'; // s_007是一个字符串      
var Answer = true; // Answer是一个布尔值true      
var t = null; // t的值是null
```


使用 var 定义的变量是局部变量，不使用var定义的变量是全局变量。我们在实际代码中应该养成用var定义变量的习惯

```
var i=10;   //局部变量      
i=10;   //全局变量
```


 JavaSscript是动态语言，使用等号`=`对变量进行赋值。可以把任意数据类型赋值给变量，同一个变量可以反复赋值，而且可以是不同类型的变量，但是要注意只能用`var`申明一次

```
var a = 123; // a的值是整数123      
a = 'ABC'; // a变为字符串
```


### **JavaScript的数据类型**

*   Number： 数字（JavaScript不区分整数和浮点数，统一用Number表示）
*   String： 字符串（单引号或双引号括起来的）
*   Boolean： 布尔值（true  false）
*   Array： 数组 （可以包含任意数据类型，并通过索引来访问每个元素。）

```
//Number      
123; // 整数123      
0.456; // 浮点数0.456      
1.2345e3; // 科学计数法表示1.2345x1000，等同于1234.5      
-99; // 负数      
NaN; // NaN表示Not a Number，当无法计算结果时用NaN表示      
Infinity; // Infinity表示无限大，当数值超过了JavaScript的Number所能表示的最大值时，就表示为Infinity
```

```
//字符串      
var a='hello,word'      
var a="hello,word"       
//多行字符串      
var b=`这是一个      
       多行      
       字符串`;       1
//如果字符串内部本身就有 ' 或者 " 的话，我们可以使用 \ 转义字符.转义字符\可以转义很多字符，比如\n 表示换行，\t 表示制表符，字符 \ 本身也要转义，所以 \\ 表示的字符就是 \      1
var b='I\'m \"OK\"!';      \\ I'm "OK"!       1
//将字符串连接起来，可以用 + 号连接：      1
var name = '小明';      1
var age = 20;      1
var message = '你好, ' + name + ', 你今年' + age + '岁了!';       1
//字符串索引取值      1
var s = 'Hello, world!';      2
s[0]; // 'H'      2
s[6]; // ' '      2
s[7]; // 'w'      2
s[12]; // '!'      2
s[13]; // undefined ，超出范围的索引不会报错，但一律返回undefined       2
//需要特别注意的是，字符串是不可变的，如果对字符串的某个索引赋值，不会有任何错误，但是，也没有任何效果：      2
var s = 'Test';      2
s[0] = 'X';      2
alert(s); // s仍然为'Test'       3
// toUpperCase()把一个字符串全部变为大写：      3
var s = 'Hello';      3
s.toUpperCase(); // 返回'HELLO'      3
toLowerCase      3
toLowerCase()把一个字符串全部变为小写：       3
// toLowerCase()把一个字符串全部变为小写       3
var s = 'Hello';      3
var lower = s.toLowerCase(); // 返回'hello'并赋值给变量lower      4
lower; // 'hello'       4
// indexOf()会搜索指定字符串出现的位置：      4
var s = 'hello, world';      4
s.indexOf('world'); // 返回7      4
s.indexOf('oor'); // 没有找到指定的子串，返回-1       4
// substring()返回指定索引区间的子串：      4
var s = 'hello, world'      4
s.substring(0, 5); // 从索引0开始到5（不包括5），返回'hello'      5
s.substring(7); // 从索引7开始到结束，返回'world'
```

```
//数组      
arr=[1, 2, 3.14, 'Hello', null, true];       
arr=new Array(1, 2, 3); // 创建了数组[1, 2, 3]       
var arr = [1, 2, 3.14, 'Hello', null, true];      
arr[0]; // 返回索引为0的元素，即1      
arr[5]; // 返回索引为5的元素，即true      
arr[6]; // 索引超出了范围，返回undefined       1
//要取得Array的长度，直接访问length属性：      1
var arr = [1, 2, 3.14, 'Hello', null, true];      1
arr.length;     // 6       1
//直接给Array的length赋一个新的值会导致Array大小的变化：      1
var arr = [1, 2, 3];      1
arr.length; // 3      1
arr.length = 6;      1
arr; // arr变为[1, 2, 3, undefined, undefined, undefined]      2
arr.length = 2;      2
arr; // arr变为[1, 2]       2
//Array可以通过索引把对应的元素修改为新的值，因此，对Array的索引进行赋值会直接修改这个Array：      2
var arr = ['A', 'B', 'C'];      2
arr[1] = 99;      2
arr; // arr现在变为['A', 99, 'C']       2
//请注意，如果通过索引赋值时，索引超过了范围，同样会引起Array大小的变化：      2
var arr = [1, 2, 3];      3
arr[5] = 'x';      3
arr; // arr变为[1, 2, 3, undefined, undefined, 'x']       3
//indexOf()  与String类似，Array也可以通过indexOf()来搜索一个指定的元素的位置：      3
var arr = [10, 20, '30', 'xyz'];      3
arr.indexOf(10); // 元素10的索引为0      3
arr.indexOf(20); // 元素20的索引为1      3
arr.indexOf(30); // 元素30没有找到，返回-1      3
arr.indexOf('30'); // 元素'30'的索引为2        4
//slice() 就是对应String的substring()版本，它截取Array的部分元素，然后返回一个新的Array：      4
var arr = ['A', 'B', 'C', 'D', 'E', 'F', 'G'];      4
arr.slice(0, 3);   // 从索引0开始，到索引3结束，但不包括索引3: ['A', 'B', 'C']      4
arr.slice(3);     // 从索引3开始到结束: ['D', 'E', 'F', 'G']       4
//push和pop    push()向Array的末尾添加若干元素，pop()则把Array的最后一个元素删除掉：      4
var arr = [1, 2];      4
arr.push('A', 'B'); // 返回Array新的长度: 4      4
arr; // [1, 2, 'A', 'B']      4
arr.pop();   // pop()返回'B'      5
arr;   // [1, 2, 'A']      5
arr.pop(); arr.pop(); arr.pop();  // 连续pop 3次      5
arr; // []      5
arr.pop(); // 空数组继续pop不会报错，而是返回undefined      5
arr; // []       5
//unshift和shift    如果要往Array的头部添加若干元素，使用unshift()方法，shift()方法则把Array的第一个元素删掉：      5
var arr = [1, 2];      5
arr.unshift('A', 'B'); // 返回Array新的长度: 4      5
arr; // ['A', 'B', 1, 2]      6
arr.shift(); // 'A'      6
arr; // ['B', 1, 2]      6
arr.shift(); arr.shift(); arr.shift(); // 连续shift 3次      6
arr; // []      6
arr.shift(); // 空数组继续shift不会报错，而是返回undefined      6
arr; // []       6
//sort()可以对当前Array进行排序，它会直接修改当前Array的元素位置，直接调用时，按照默认顺序排序：      6
var arr = ['B', 'C', 'A'];      6
arr.sort();      7
arr; // ['A', 'B', 'C']       7
//reverse()把整个Array的元素给掉个个，也就是反转：      7
var arr = ['one', 'two', 'three'];      7
arr.reverse();       7
arr;   // ['three', 'two', 'one']       7
//splice()方法是修改Array的“万能方法”，它可以从指定的索引开始删除若干元素，然后再从该位置添加若干元素：      7
var arr = ['Microsoft', 'Apple', 'Yahoo', 'AOL', 'Excite', 'Oracle'];// 从索引2开始删除3个元素,然后再添加两个元素:      7
arr.splice(2, 3, 'Google', 'Facebook');   // 返回删除的元素 ['Yahoo', 'AOL', 'Excite']      8
arr; // ['Microsoft', 'Apple', 'Google', 'Facebook', 'Oracle']      8
arr.splice(2, 2); // ['Google', 'Facebook']   // 只删除,不添加:      8
arr; // ['Microsoft', 'Apple', 'Oracle']      8
arr.splice(2, 0, 'Google', 'Facebook'); // 只添加,不删除: 返回[],因为没有删除任何元素      8
arr; // ['Microsoft', 'Apple', 'Google', 'Facebook', 'Oracle']       8
//concat()方法把当前的Array和另一个Array连接起来，并返回一个新的Array：      8
var arr = ['A', 'B', 'C'];      8
var added = arr.concat([1, 2, 3]);      8
added; // ['A', 'B', 'C', 1, 2, 3]      9
arr; // ['A', 'B', 'C']      9
请注意，concat()方法并没有修改当前Array，而是返回了一个新的Array。       9
//实际上，concat()方法可以接收任意个元素和Array，并且自动把Array拆开，然后全部添加到新的Array里：      9
var arr = ['A', 'B', 'C'];      9
arr.concat(1, 2, [3, 4]); // ['A', 'B', 'C', 1, 2, 3, 4]       9
//join()方法是一个非常实用的方法，它把当前Array的每个元素都用指定的字符串连接起来，然后返回连接后的字符串：      9
var arr = ['A', 'B', 'C', 1, 2, 3];      9
arr.join('-'); // 'A-B-C-1-2-3'   //如果Array的元素不是字符串，将自动转换为字符串后再连接。       10
//多维数组      10
如果数组的某个元素又是一个Array，则可以形成多维数组，例如：      10
var arr = [[1, 2, 3], [400, 500, 600], '-'];
```


**逻辑运算符**：&&    ||    ! 

**比较运算符**：<    <=      >    >=   ==    ===  

注：第一种是`==`比较，它会自动转换数据类型再比较，很多时候，会得到非常诡异的结果；

       第二种是`===`比较，它不会自动转换数据类型，如果数据类型不一致，返回`false`，如果一致，再比较。建议用这种运算符判断是否相等

**空值：**`null`表示一个“空”的值，它和`0`以及空字符串`''`不同，`0`是一个数值，`''`表示长度为0的字符串，而`null`表示“空”。

### **对象** 

 JavaScript的对象是一组由键-值组成的无序集合，例如：

```
var person = {      
    name: 'Bob',      
    age: 20,      
    tags: ['js', 'web', 'mobile'],      
    city: 'Beijing',      
    hasCar: true,      
    zipcode: null,      
    'sex': 'men',   //特殊属性       1
    age: function () {         //方法      1
        var y = new Date().getFullYear();      1
        return y - this.birth;      1
    }      1
};
```


注意：最后一个键值对不需要在末尾加`,`，如果加了，有的浏览器（如低版本的IE）将报错 

JavaScript对象的键都是字符串类型，值可以是任意数据类型。上述`person`对象一共定义了6个键值对，其中每个键又称为对象的属性，例如，`person`的`name`属性为`'Bob'`，`zipcode`属性为`null`。

要获取一个对象的属性，我们用`对象变量.属性名`的方式：

```
person.name;           // 'Bob'      
person.zipcode;        // null      
person['sex']          //特殊属性
```


**如果访问一个不存在的属性会返回什么呢？**

```
console.log(person.telphone);   //undefined
```


 **动态给对象增加删除属性**

```
 var person={      
            name:"xie"      
};      
person.age=20;    //给person对象添加一个age属性      
console.log(person.age);       
delete person.age;  //删除掉person对象的age属性      
console.log(person.age);
```


**判断对象是否有某个属性** 

```
var xiaoming = {      
    name: '小明',      
    birth: 1990      
};      
//如果我们要检测xiaoming是否拥有某一属性，可以用in操作符：      
'name' in xiaoming; // true      
'grade' in xiaoming; // false      
//不过要小心，如果in判断一个属性存在，这个属性不一定是xiaoming的，它可能是xiaoming继承得到的：      
'toString' in xiaoming; // true      1
//因为toString定义在object对象中，而所有对象最终都会在原型链上指向object，所以xiaoming也拥有toString属性。       1
//要判断一个属性是否是xiaoming自身拥有的，而不是继承得到的，可以用hasOwnProperty()方法：      1
var xiaoming = {      1
    name: '小明'      1
};      1
xiaoming.hasOwnProperty('name'); // true      1
xiaoming.hasOwnProperty('toString'); // false
```


BOM浏览器对象模型
----------

![](https://img-blog.csdnimg.cn/20181107170543697.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

*   最高层是窗口对象（window），它代表当前的浏览器窗口。
*   浏览器窗口下级又包括文档（document）、事件（event）、历史（history）、URL地址（location）、浏览器（navigator）和屏幕（screen）对象。
*   部分二级对象下还有下级，如文档（document）还有applet、area、form等，form下级还有button、checkbox等。

使用BOM模型中的对象

在浏览器对象模型的层次结构中，所有下层对象都是其上层对象的子对象。而子对象其实就是父对象的属性，所以引用子对象的方式，与引用对象的一般属性是相同的。例如：引用文档对象，  window.document.write(“hello”);

注：由于window对象是默认的最上层对象，因此引用它的子对象时，可以不使用 window，如： document.write(“hello”);

**window对象的常用事件**

![](https://img-blog.csdnimg.cn/20181107171436142.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### ![](https://img-blog.csdnimg.cn/2019030113360117.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### DOM文档对象模型

在 HTML DOM 中，所有事物都是节点。DOM 是被视为节点树的 HTML

*   整个文档是一个文档节点
*   每个 HTML 元素是元素节点
*   HTML 元素内的文本是文本节点
*   每个 HTML 属性是属性节点
*   注释是注释节点

![](https://img-blog.csdnimg.cn/20181107195753833.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### HTML DOM 对象 - 方法和属性

由于HTML文档被浏览器解析后就是一棵DOM树，要改变HTML的结构，就需要通过JavaScript来操作DOM。

始终记住DOM是一个树形结构。操作一个DOM节点实际上就是这么几个操作：

*   更新：更新该DOM节点的内容，相当于更新了该DOM节点表示的HTML的内容；
*   遍历：遍历该DOM节点下的子节点，以便进行进一步操作；
*   添加：在该DOM节点下新增一个子节点，相当于动态增加了一个HTML节点；
*   删除：将该节点从HTML中删除，相当于删掉了该DOM节点的内容以及它包含的所有子节点。

一些常用的 HTML DOM 方法：

*   getElementById(id) - 获取带有指定 id 的节点元素
*   getElementByName(name) - 获取指定name的节点元素
*   getElementByClassName(classname) - 获取指定classname的节点元素
*   getElementByTagName(Tag) - 获取指定Tag标签的节点元素
*   appendChild(node) - 插入新的子节点（元素）
*   removeChild(node) - 删除子节点（元素）

一些常用的 HTML DOM 属性：

*   innerHTML - 节点（元素）的文本值
*   parentNode - 节点（元素）的父节点
*   childNodes - 节点（元素）的子节点
*   attributes - 节点（元素）的属性节点

![](https://img-blog.csdnimg.cn/20181107195857162.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181107195922111.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

例：

**改变html内容**

```
x=document.getElementById("demo")  //查找id为demo的元素，      
x.innerHTML="Hello JavaScript";    //改变其内容
```


**改变html图像**

```
element=document.getElementByName("demo");   //查找name为demo的元素      
element.src=”img/1.jpg”;            //将其src改变
```


**改变html的样式**

```
x=document.getElementById("demo")  //找到id为demo的元素      
x.style.color="#ff0000";           //改变样式
```


**创建新的html元素**

```
<div id="div1">      
<p id="p1">这是一个段落</p>      
<p id="p2">这是另一个段落</p>      
</div>      
<script>      
var para=document.createElement("p");      
var node=document.createTextNode("这是新段落。");      
para.appendChild(node);      
var element=document.getElementById("div1");      1
element.appendChild(para);      1
</script>
```


**删除已有的html元素**

```
<div id="div1">      
<p id="p1">这是一个段落。</p>      
<p id="p2">这是另一个段落。</p>      
</div>      
<script>      
var parent=document.getElementById("div1");      
var child=document.getElementById("p1");      
parent.removeChild(child);      
</script>
```


### DOM操作表单

我们可以用JavaScript操作表单，获取表单的值或者填入一个值

HTML表单的输入控件主要有以下几种：

*   文本框，对应的`<input type="text">`，用于输入文本；
*   口令框，对应的`<input type="password">`，用于输入口令；
*   单选框，对应的`<input type="radio">`，用于选择一项；
*   复选框，对应的`<input type="checkbox">`，用于选择多项；
*   下拉框，对应的`<select>`，用于选择一项；
*   隐藏文本，对应的`<input type="hidden">`，用户不可见，但表单提交时会把隐藏文本发送到服务器

**获取值**

如果我们获得了一个`<input>`节点的引用，就可以直接调用`value`获得对应的用户输入值：

```
<input type="text" id="email">       
var input = document.getElementById('email');      
input.value; // '用户输入的值'
```


这种方式可以应用于`text`、`password`、`hidden`以及`select`。但是，对于单选框和复选框，`value`属性返回的永远是HTML预设的值，而我们需要获得的实际是用户是否“勾上了”选项，所以应该用`checked`判断：

```
<label><input type="radio" name="weekday" id="monday" value="1"> Monday</label>       
var mon = document.getElementById('monday');      
mon.checked; // true或者false
```


**设置值**

设置值和获取值类似，对于`text`、`password`、`hidden`以及`select`，直接设置`value`就可以：

```
<input type="text" id="email">      
var input = document.getElementById('email');      
input.value = 'test@example.com'; // 文本框的内容已更新
```


对于单选框和复选框，设置`checked`为`true`或`false`即可

**表单提交**

```
//方法一：      
<!-- HTML -->      
<form id="test-form" onsubmit="return checkForm()">      
    <input type="text" name="test">      
    <button type="submit">Submit</button>      
</form>       
<script>      
function checkForm() {      1
    var form = document.getElementById('test-form');      1
    return true;      1
}      1
</script>
```

```
//方法二：      
<!-- HTML -->      
<form id="test-form">      
    <input type="text" name="test">      
    <button type="button" onclick="doSubmitForm()">Submit</button>      
</form>       
<script>      
function doSubmitForm() {      1
    var form = document.getElementById('test-form');      1
    form.submit();      1
}      1
</script>
```
