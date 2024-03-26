# Javascript

----

[toc]

## 内嵌Html
  
- __`<script>`标签__
    属性 :
    |名称 | 作用|
    |--|--|
    | type| 指定脚本内容的类型, 通常是text/javascript|
    | defer| 表示脚本可以延迟到文档完全被解析后再执行|
    | async | 表示应该立即下载脚本, 但不影响网页中的其他操作(异步)|  

- __在`<script>`中直接编写代码__
    如果在 `<script>` 中直接写代码, 可能会造成一些意外的错误

```js
    function compare(a, b) {
        if (a < b) {
            alert("a < b");
        } else if (a > b) {
            alert("a > b");
        } else {
            alert("a == b");
        }
    }
```

html解析器在遇到第一个`<`时就会将其解析为一个标签, 标签后无法跟空格,于是翻译有错.
我们可以利用 `CData`来对代码段进行包装.

```html
    <script defer="defer"> 
        <![CData[
            function sayHi(): void {
                alert("Hi<");
            } 
        ]]>
    </span>    
```

## 基本语法

## 严格模式

在顶部添加 "use strict" 会使整个脚本启用严格模式, 如 :

```javascript
    function hello() : void {
        "use strict";
        // body
            ... 
    }
```

## 变量

ECMAScript(javascript) 的变量是松散类型的, 即每个变量都是`std::any`, 每个变量仅仅是一个保存变量的占位符罢了
变量使用`var`进行声明 :

```javascript
    var message = "hi";
    var a; // a的值为 "undefined"
    message = 100;
```

每个变量都有各自的作用域,超出了就会被销毁.

但是可以通过省略`var`的方法来声明全局变量, 如下面的`hello`, 只要调用一次`hello`, 那么变量`message`就会一直存在

```javascript
    function hello() {
        message = 100;
    }
    hello();
    alert(message);
```

## 数据类型

- ECMAScript 有5种基本数据类型 :
  - Undefined : 但没有声明一个值或声明了没有赋初值时, 只有一种值 : `undefined`
  - Null :  空对象指针, 只有一种值 `null`
  - boolean : true | false
  - number : 转false为0, true 为1
  - string : 转false为空, true 为非空
  - object : 转false为null

- `typeof`
typeof 可以检测数据类型, 分别是
  
  - Undefined : 这个值未定义
  - boolean : 这个值是布尔值
  - string : 这个值是字符串
  - number : 这个值是数值
  - object : 这个值是对象或者null
  - function : 这个值是函数

- 数值 :

1. 整数值
`number`类型可以通过八进制(整数前加上0o), 十进制, 十六进制(整数前加0x)来表示

```js
 var number = 1;
 var number_oct = 0o10;   //八进制
 var number_hex = 0x3f;  // 十六进制
```

2. 浮点数值

`float`浮点类型是通过数值 + 小数点实现, 也可以用科学计数法表示, 但由于浮点类型的
精度影响, 容易导致出现 0.1 + 0.2 != 0.3 的局面, 借此我们可以通过将等式两边差的
绝对值与`Number.EPSILON`进行比较, 小于这个阙值的一律看作0即可.

3. 数值范围
   数值范围保存在`Number.MAX_VALUE` 以及 `Number.MIN_VALUE`中, 超出范围将被转为`Infinity` 和 `-Infinity`

4. NaN
    NaN表示数值方面的错误, 如除数为0, 无法转为数值类型等.
    数值`NaN == NaN` 的结果为 `false`, 需要通过 `isNaN()` 函数来判断.

    ```js
        isNaN(NaN) // 结果为true
    ```

5. 数值转换
    - Number()
      - 如果是Boolean, `true` 和 `false` 被分别转化为 1, 0
      - 如果是数值(number)类型, 则直接返回
      - 如果是null值, 则返回0
      - 如果是undefined, 则返回NaN
      - 如果是字符串 :
        - 如果只包含数字, 则转为十进制
        - 如果包含浮点数, 则转浮点数
        - 如果包含有效十六进制的值, 则转16进制
        - 如果是空的, 则转为0
        - 否则, 转NaN  

    - parseInt()
        - parseInt() 按顺序转化数字直到遇到第一个非数字(不含浮点)
        - 如果第一个非数字前的串符合16进制写法, 则转为16进制
        - 否则值为NaN
        我们可以为parseInt()传入一个基数 `base`, 比如传入16时, 解析的时候便无需添加前缀`0x`

    - parseFloat()
        parseFloat() 同parseInt, 只不过是用来识别float类型, 即第一个小数点有效
        此外 parseFloat() 会直接忽略前导0支持科学计数法

- 字符串
    由0或多个16为Unicode字符组成的字符序列, 支持转义
    字符串的长度可以由属性`length`来取得, 支持拼接如 :

  ```js
    var s1 = "0000000";
    s1 += "s1";
    s1 += s1;
    s1 += 1;
    s1 += true;
    s1 += 0x3f; // 0x3f被拼接后为63
  ```

- 支持利用 toString() 函数进行转换, 但`null` 和 `undefined`没有这个方法
- 支持利用 String()   函数进行转换构造, 如`null` => "null", `undefined` => "undefined"

- Object 类型
  Object 即一组数据和功能的集合, 可以通过new来创建相应的对象

```js
    var obj = new Object();         
    var obj = new Object;         
```

  其中类Object 是通过这种方法创建的所有类的原型, 所以每个实例都有以下方法 :

- Constructor : 保存用来创建当前对象的函数
- hasOwnProperty(propertyName : String) 用于查给定属性是否存在当前实例中(不是原型)
- isPrototypeOf(Object) 判断传入的对象是不是另一个对象的原型
- propertyIsEnumerable(propertyName : String) 用于检查给定属性是不是可以利用`for-in`来枚举
- toLocalString() : 返回对象的字符串表示, 类型同本地环境
- toString() : 返回对象的字符串表示
- valueOf() : 返回对象的字符串, bool, 数值表示一般同toString()

## 操作符

- ++ 和 --
    基本对标java和C的++和-- :
  - 在应用于一个包含有效数字的字符串时, 先将其转化为数字值, 再执行 + 1操作
  - 不包含有效数字时, 则字符型转为数值型, 值为NaN
  - 应用于Boolean时, 转为数字类型, 再 + 1, 布尔型转为字符型
  - 应用于对象时, 先调用valueOf(), 应用前述规则, 如结果是NaN, 则调用toString(), 对象变量转为数值

- 一元-, +
    数值正常, 非数值会参照 `++` 和 `--`的规则尝试转为数值, 否则为NaN
- 位操作
    数值正常运算, 非数值同上, 其余同java
- 布尔操作
  - 当操作为 `!`时, 变量会被转为`boolean`类型进行操作
  - 当操作为`&&`时, 非对象则转Boolean
    - 第一个操作数是对象, 返回第二个操作数(任意类型)
    - 第二个操作数是对象, 只有在第一个操作数的求值结果为true的情况下返回该对象, 否则为`false`
    - 有一个操作数是`null`/`undefined`/`NaN`则返回`null`/`undefined`/`NaN`

    `&&` 为短路操作如果第一个表达式是`false` 则第二个操作时甚至可以未定义
    这意味着你甚至可以这么写代码 :

    ```js
        console.log((false && (dwc+ef2&&ed2^e1CS%SK%e%ywarg.consoleuments.length.o`````r```d``---1Va1lue,wd,w1`````)));
    ```

    - 操作为 `||`时, 与 && 同理

- 算术运算符 :
  - `*`
    - 如果乘积超过范围, 则结果为`infinity` or `-infinity`
    - 如果有一个操作数为`NaN`, 则结果为`NaN`
    - 如果是`infinity * 0` 结果为`NaN`, 否则为`infinity` or `-infinity`
    - 非数值转数值计算

  - `/`
    - 同 `*`
    - `infinity / inifinity == NaN`
    - `0 / 0 == NaN`
    - `非0 / 0 == (+/-)infinity`
    - `infinity / 数值 == (+/-)infinity`
    - 非数值转数值计算

  - `%`
    - `infinity % x == NaN`
    - `x % infinity == x`
    - `infinity % infinity == NaN`
    - 非数值转数值计算  

  - `+`
    - `(+/-)infinity + (+/-)infinity == (+/-)infinity`
    - `(-/+)infinity + (+/-)infinity == NaN`
    - `+0 + (+/-)0 == +0`
    - `-0 + -0 == -0`
    - 字符串
      - 非字符串转字符串(对象, 布尔, 数值 toString()， null, undefined String())

  - `-`
    - 第二个操作数符号变一下
    - 字符串, 布尔, 对象转数值
    - 对象调用valueOf(), 没有就调用toString()

- 比较运算符 :
  - 有一个是数值, 另一个转数值
  - 有一个是对象, 调用valueOf

- `==` 和 `===`
  - bool `false => 0` `true => 1`
  - 字符和数值, 字符=>数值 (比如 '5' == 5)
  - 对象, valueOf()
  - null == undefined != 0

  - ===要求类型相同下的比较

- `,` , `?`同java
- `for`, `while`, `do-while` 同java
- `for-in`
  用来枚举对象的属性

  ```js
        for (var propName in window) {
            document.write(propName)
        }
    
  ```

- `label` 语句同java适配`continue`和`break`

- `with`
   with的作用是将代码的作用域设置到一个特定的对象中, 语法如下 :

   ```javascript
        with(expression) statement 

   ```

   用法如下 :

    ```js
        var qs = location.search.substring(1);
        var hostName = location.hotname;
        var url = location.href 
        // 可以转化为这样
        with (location) {
            var qs = search.substring(1);
            var hostName = hotname;
            var url = href 
        }
    ```

    使用with惯量了location变量, 这意味着在with的代码块中, 每个变量被首先认为是一个局部
    变量, 如果找不到, 则认为是 with(exp)中, exp的属性注意 __严格__ 模式下不允许使用
    `with`.

- switch
  ECMAScript 中的switch与C不同的是, 它可以使用变量或表达式 :
  
  ```js
       switch(true) {
            case num >= 0 : { ... break;} 
            case num == 0 : { ... break;} 
            case num === 0 : { ... break;} 
            case num < 0 : { ... break;}
            default : {
                ...
            } 
       } 

       switch(str) {
            case "GOODBYE" + "HELLO" : { ... break;}
       }

  ```

    注意 __switch比较时使用的是全等(===)__ 运算符, 不会隐式转换

## 函数

函数由 function *function_name* (*args_list*) { *statement* } 构成
允许右默认参数, 允许闭包

```js
    var sum = function(a, b = 1) {
        return a + b;
    };

    function sub(a, b) {
        return a - b;
    };   
```

在ECMAScript中, 函数的参数类型和数量都可以不确定, 因为函数最终接受的是一个名为 `arguments` 的数组对象
这个数组中的大小可以不确定, 你甚至可以在函数里打印这个 `argument`对象.

```js
    function printArgs() {
        for (var i = 0; i < arguments.length; i++) {
            console.log(arguments[i]);
        }
    }
```

所以在ECMAScript中, 函数的参数只起到提示作用而已.
其次, ECMAScript函数传递的都是值, 不存在引用传递.

- __不支持重载__
    ECMAscript 不支持函数重载, 多次声明以最后一个符号为准.

## 变量, 作用域, 以及内存问题

- 动态类型, 可以使用`new`运算符来声明动态类型, 并创建它的属性.

``` javascript
    var a = 1;
    var b = new Object();

    b.name = "NiCholas";
```

- 复制变量的值
当复制对象为`Object`动态类型时, 复制对象的值采用引用, 而基本类型采用值复制.
在函数传参时同理, 但当我们为这个传进来的参数重新提供一个值的时候, 对象变为局部对象
原引用保持不变.

```js
    var c = new Object(); 
    c.name = "NiCho";

    var d = c;
    d.name = "NiCholas";

    d = new Object();
    d.name = "NiBuChuo";
```

- __检测类型__
利用`typeof`关键字检测变量的类型, 也可以利用 `instanceof`来检测变量是否是给定引用
类型的实例, `typeof` 对所有引用类型类型都返回 `object`, 而`instanceof`却可以检测
其具体是哪种类型的实例.

### 执行环境

javascript执行环境中定义了变量或函数有权访问的其他数据, 每个执行环境都有它们与之关联的全局
变量,在web服务器中, 这个全局变量通常被认定为`windows`

当代码在一个环境中执行时, 会创建变量的作用域链, 在查找变量时, 会先查找当前作用域, 如果没有
找到, 再逐层向上查找。

在`with`语句中声明的变量, 其作用域会延长到外部(高一级)作用域.

javascript中不存在块级作用域, 在`for`声明的变量仍然会延续到外部作用域.

```js
    var a = 1;
    for (var i = 0; i < 10; i++) {
        console.log(i);
    }

    console.log(i); // 10
```

在声明变量时, 如果使用`var`关键字, 其作用域为最接近的作用域, 否则被添加进全局作用域中.

- 垃圾回收
  - 标记清除
    打上相应的标记, 如果再次被打上对应标记, 那么垃圾收集器就会对该内存进行回收.
  - 引用计数
    引用计数容易出现循环引用的问题

## 引用类型

## 创建对象实例

- 构造函数
  
  ```js
        var a = new Object();
  ```

- 对象字面量

  ```js
        var a = {
            name : "NiCho",
            age : 18
        };
  ```

## 对象访问

javascript中, 可以采用两种方法访问对象的属性, 点表示法和`[]`表示法.

```js
      var a = {
          name : "NiCho",
          age : 18
      };

      var aproperty = "name";
      console.log(a.name);
      console.log(a["name"]);
      console.log(a[aproperty]);
```

`[]`括号表示法的优点是可以通过变量索引来访问对象的属性, 同时可以包含非法字符

```js
      var a = {
          name : "NiCho",
          age : 18
      };
      var aproperty = "first name";
      console.log(a[aproperty]);
```

## Array

## array 的创建方式

- 构造函数

    可以通过`new`来创建一个array对象
- 字面量
  
    可以通过`[]`来创建数组

数组访问数组可以通过`[]`来访问, `length`来获取长度, 其中, `array.length`不是只读的
可以通过这个变量来resize.

## 检测数组

使用`instanceof`可以方便的判断一个对象是不是array, 但`instanceof`始终假定只有
一个全局环境, 所以我们可以用Array.isArray()来判断一个对象是不是array.

## 数组行为

- 对array调用`toString()`时, 会得到所有成员`toString()`的拼接结果, 而调用`valueOf`返回的还是数组.

### 栈方法

  数组的行为可以表现得像栈一样, 因为array提供了如下函数

- push()
- pop()
  
### 队列方法

- shift()
- push()
- unshift() : push_front()

### 重排序方法

- reverse()
- sort()

 其中, sort 接受一个谓词, 用来对元素进行比较, -1 为a 应该排b之前, 0 为相等, 1 为b之后

### 操作

- concat() 返回一个append所有参数之后的原数组
- slice()  返回一个子数组
- splice()  
  - 删除 : 传入起始位置与长度
  - 插入 : 起始位置, 要删除的项数, 要插入的项

### 位置方法 :  起始位置, 值

- indexOf() 从前往后
- lastIndexOf() 从后往前

### 迭代

- every() 对每一项运行给定函数, 如果都为true, 则返回trues
- filter() 对每一项运行给定函数, 返回为true的项组成的数组
- forEach() 对每一项运行给定函数
- map() 对每一项运行给定函数, 返回运行结果组成的新数组
- some() 只要有一项为true, 则返回true

### 缩小方法 (ECMAScript 5)

- reduce()  接受一个函数对象 :
  - function(prev, cur, index, array) 迭代所有值返回一个结果
- reduceRight() 从尾到头

## Date类型

  使用`new`来创建Date对象, 如果不指定参数, Date对象将自动获取当前时间(UTC)

- Data对象传入时间
      - Data.parse()
        - 接受一个表示日期的字符串
      - Data.UTC()
        - 年份
        - 从0开始的月份
        - 天数
        - 小时数
        - 分钟数
        - 毫秒数
        - 缺省值为0
      - Data.now() 当前时间
  
  - 格式化
    - toDateString()
    - toTimeString()
    - toLocalDateString()
    - toLocalDateString()
    - toLocalTimeString()
    - toUTCString()
    - toLocaleString()

## RegExp

  javascript 支持正则表达式库

  声明方式为 :
    *var expression = / pattern / flag ;*
  
  其中flag :
  |Flag | 作用 |
  |--|--|
  | g |  全局模式   即模式被用于全部字符串, 而不是发现第一个匹配后停止|
  | i | 匹配时不区分大小写|
  | m | 多行模式, 到达一行文本末尾是否继续查找下一行|

  在创建正则表达式时, 特殊字符仍需转义

  ```js
    /*
        匹配第一个cat / bat 不区分大小写
    */
    var pattern1 = /[bc]at/i;
    /*
        匹配所有以at为结尾的3字符组合, 不区分大小写
    */
    var pattern2 = /.at/gi;
    /*
        匹配所有.at, 不区分大小写
    */
    var pattern3 = /\.at/gi
   
  ```

如果像将特殊字符当作普通字符进行匹配, 则需要转义'\\'.
方法exec() 每次会匹配一段字符串后(全局模式)对上一次匹配的信息进行保存, 下一次调用时, 会从当前状态开始(即使换了字符串)

捕获组, 可以用 ()声明捕获组, 并通过$[1-10]来访问它们 (已被弃用)

## ECMAScript不支持的特性

ECMAScript不支持的特性 :

- 匹配字符串开始和结尾的\A和\Z锚 (但支持^和$为结尾的锚)
- 向后查找
- 并集和交集类
- 原子组
- Unicode支持
- 命名的捕获组(但支持以编号命名的捕获组)
- s(单行) 和 x(无间隔) 模式匹配
- 条件匹配
- 正则表达式注释

## Function

## 函数的创建

```js
    function sum(s1, s2) {
        return s1 + s2;
    }

    var sum = function(s1, s2) {
        return s1 + s2;
    };


    // 函数其实是一个对象
    var sum = new function("s1", "s2", "return s1 + s2");

```

> ECMAScript 的解释器在执行代码之前会读取并将函数声明提取到源代码树(也就是执行环境的顶部), 所以允许函数定义在调用之后.

```js
    console.log(sum(1, 2));
    
    function sum(s1, s2) 
    { return s1 + s2 }
```

当然这仅限函数式声明的时候, 利用对象创建的方式(即函数表达式)则无用
这是因为函数表达式变量使得函数定义在一个初始化体内, 在该语句被执行前,
sum仍然指向null.

## 捕获

- 在函数内部声明一个函数时外部函数的arguments可以被闭包所捕获即

```js
    function createComparisonFunction(propertyName) {
        return function(object1, object2) {
            var v1 = object1[propertyName];
            var v2 = object1[propertyName];

            if (v1 < v2) {
                return -1;
            } else if (v1 > v2) {
                return 1;
            } else {
                return 0;
            }
        };
    }
```

## callee (discarded)

函数实际上是一个指针, 这导致我们在使用时, 容易使得结果随指针的内容
变化而出错 :

```js
    function factorial(num) {
        if (num <= 1) {
            return 1;
        } else {
            return num * factorial(num - 1);
        }
    }


    var tmp = factorial;
    factorial = function(s1) { return 0; };

    console.log(tmp(10));
```

随着上式的调用, 由于函数内部调用的依然是factorial, 则结果会出错
所以可以利用`callee`来解决这个问题
改成 num * argument.callee(num - 1);
即调用拥有这个参数列表的函数(现在是tmp)

## caller

  返回调用这个函数的函数

## this

函数内部引用当前函数所属的对象, 在全局作用域被调用, 即windows对象

## 函数的属性

- Length 即函数希望接受的参数个数
- prototype

- apply 与 call

    两者可以通过设置作用域来跨作用域调用, 接受一个this对象与参数列表(如果有的话)
    可以利用设置的this来扩充作用域

- bind(ECMAScript 5)
    通过调用bind属性可以返回一绑定了特定this的对象.

## 包装类型

指在利用基本类型调用一些方法时, 如String的substring(), 解释器会隐式生成一个String 的对象来调用

## Boolean

不推荐

## Number

- toFixed( : Number) 返回特定位数四舍五入后的字符串表示
- toExponential( : Number) 科学计数法
- toPrecision( : Number) 视具体情况表示 如传入1的话99 => 1e+2

## String

- length()
- charAt(), charCodeAt()
- concat()
- slice(begin : Number, end : Number) 传入负数则与length相加
- substr(begin : Number, length : Number) 传入负数第一个与length相加, 第二个转为0
- substring(begin : Number, end : Number) 传入负数全部转为0
- indexOf()
- lastIndexOf()
- trim() 返回删除了前缀与后缀空格的副本
- toLowerCase(), toLocaleLowerCase()
- toUpperCase(), toLocaleUpperCase()
- match(pattern : RegExp) 与exec()相同,返回匹配结果与捕获列表组成的Array
- search() 返回找到的第一个匹配索引
- replace(pattern : RegExp, source : String) 替换所有匹配项
  - replace()方法的第二个参数允许带一些特殊字符来保存捕获到的信息

    |表达式|结果|
    |-|-|
    |$$|$ |  
    |$&|匹配整个模式的子字符串即RegExp.lastMatch|  
    |$'|匹配的子字符串之前的字符串即RegExp.leftContext'|  
    |$`|匹配的子字符串之后的字符串即RegExp.rightContext`|
    |$n(n)?|捕获列表|

- replace(pattern : RegExp, f : function(match, pos, originText)) 这允许根据匹配的值进行更加细致化的输出
- spilt(str : RegExp or String, length : Number) 其中第二个参数用来指定返回的数量限制
- localCompare(str : String) 大于为 1 小于为 -1, 等于为 0
- fromCharCode() 根据传入的编码构造字符串

## 单体内置对象

## Gobal

- encodeURI()   对特殊字符进行编码
- encodeURIComponent()  
- eval() 相当于宏替换

## Math对象

- min(), max()
- round(), ceil(), floor()
- random() (0, 1)

## 面向对象程序设计

数据属性 \[[*Configurable*]], \[[*Enumerable*]], \[[*Writable*]], \[[*Value*]]

- Object.defineProperty(obj : Object, propertyName : String, any)

```js
    var person = {};
    Object.defineProperty(person, "name", {
        Value : "NiChiFanleMa",
        writable : false,    // this property is read only
        configurable : false // this property cannot be deleted 
    });
```

访问器属性 \[[*Configurable*]], \[[*Enumerable*]], \[[*Get*]], \[[*Set*]]

其中Get与Set为被修改或者被读取时调用的函数

- Object.defineProperties(obj : Object, expression : any)
该函数可以一次修改多个属性,

```js
    var person = {};
    Object.defineProperty(person, {
        name : {
          Value : "NiChiFanleMa",
          writable : false,    // this property is read only
          configurable : false // this property cannot be deleted 
        },

        _year : {
            Value : 2023,
            writable : false, // 私有对象不支持修改
            configurable : false
        },

        year : {
            get : function() {
                return this._year;
            },

            set : function(newValue) {
                this.year = newValue;   
            }
        }   
    });
```

- Object.getOwnPropertyDescriptor()

这个函数可以获取一个属性的各个访问属性

## 创建对象

## 简单工厂模式

  虽然javascript无法直接创建对象, 但可以利用函数来封装对象的创建

```js
    function createPerson(name, age, job) {
        return {
            name : {name},
            age  : {age},
            job : {job},

            sayName : function() {
                console.log(this.name);
            }
        };
    }
```

## 构造函数模式

```js
    function Person(name, age, job) {
        this.name = name;
        this.age = age;
        this.job = job;
        this.sayName = function() {
            console.log(this.name);
        }
    }

```

利用这种方法声明的一个对象有一个constructor属性 即Person
对象的instanceof 可以为Person 或 Object

构造函数也可以用来修改对象属性

```js
    function Person(name, age, job) {
        this.name = name;
        this.age = age;
        this.job = job;
        this.sayName = function() {
            console.log(this.name);
        }
    }

    var o = {};
    Person.call(o, "My name", "age", "job");
    console.log(name); // My name;

```

由于每次调用构造函数都将创建一份新的函数实例, 于是我们可以将成员函数的定义放在外面, 这样就可以使得所有实例都指向同一份函数.

```js
    function Person(name, age, job) {
        this.name = name;
        this.age = age;
        this.job = job;
        this.sayName = sayName;
    }

    function sayName() {
        console.log(this.name);
    }

    var o = {};
    Person.call(o, "My name", "age", "job");
    console.log(name); // My name;

```

## 原型模式

我们可以通过直接设置构造器的prototype属性来使得所有构造出来的对象都有相同的默认属性.

```js
    function Person() {};
    Person.prototype.name = "nihao"; // 也可以使用 {} 来声明多个属性

```

这样, 当对象没有设置name, 或者对象delete掉了这个属性时, 就会恢复成原型

- prototype.isPrototypeOf() 判断该属性是否属于某个对象
- Object.getPrototypeOf()   得到对象的prototype
- obj.hasOwnProprty()   判断某个属性是否只属于某个实例

- in 操作符
  - 判断某个属性是否属于某个对象(原型的默认值也算)

for-in 操作会枚举对象所有可访问的可枚举的属性(包括原型)

可以使用Object.keys(person.prototype) 来得到所有访问属性

- 原型的prototype为动态属性, 即当原型的属性改变时, 其实例对象会同步更新

- 利用构造函数模式 + 原型模式, 可以实现一个对象的公共属性与私有熟悉的同时创建,
同时, 我们可以在构造函数中利用instanceof来判断构造函数是否是初次调用来动态加载prototype

```js

    function Person(name, age, job) {
        this.name = name;
        this.age = age;
        this.job = job;

        if (this.sayName instanceof function) {
            this.prototype.sayName = function() {
                    console.log(this.name);
            };
        }
    }
```

## 寄生构造函数

  其原理为使用一个函数封装一个实例添加额外属性的过程, 与工厂模式基本一致

## 稳妥构造函数

  创建对象并初始化属性时不用this, 调用构造函数时不用new的创建过程

## 继承

## 原型链

我们可以通过原型链的方式实现继承, 即可以将派生类的prototype 设置为基类实例来做到

```js
    function base() { }
    base.prototype = {
        name : "NiHao",
        property : true
    };

    function derive() { }
    derive.prototype = new base();



```

但是, 由于这种方法只是创建基类对象的实例并创建基类指针而已, 所以所有派生类实例的基类指针都将
只有一份, 这意味着当某一个派生类的实例修改了与基类相关的属性时, 所有的实例都会被统一修改.

## 借用构造函数

我们可以利用基类的构造函数(前面说了可以利用apply或call来改变this)

```js
    function SuperType() {
        this.name = "niHao";
    }

    function SubType() {
        SuperType.call(this);
    }

    var p1 = new SubType();
```

## 组合继承 (借用构造函数 + 原型链)

我们可以将不会改变(或者需要同步更新)的部分放在原型中, 会改变的部分放在属性中, 采用经典继承与
原型链相结合的方式实现继承.

## 原型式继承