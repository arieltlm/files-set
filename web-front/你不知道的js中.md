[toc]

## 1.数组

* 1.使用delete可以删数组元素，但是数组长度不变

  ```js
  const arr = [1,2,3]
  delete arr[1]
  
  arr // [1,empty,3]
  ```

  

## 2.3 数字

* 1、特别大和特别小的数字默认用指数格式显示，与 toExponential() 函数的输出结果相同

  ```js
  var a = 5E10;
  a;                  // 50000000000
  a.toExponential();  // "5e+10"
  typeof(a.toExponential()); // string
  ```

* 2、toPrecision(..) 方法用来指定有效数位的显示位数

  ```js
  var a = 42.59;
  a.toPrecision( 1 ); // "4e+1"
  a.toPrecision( 2 ); // "43"
  a.toPrecision( 3 ); // "42.6"
  a.toPrecision( 4 ); // "42.59"
  a.toPrecision( 5 ); // "42.590"
  a.toPrecision( 6 ); // "42.5900"
  ```

* 3、`. `运算符需要给予特别注 意，因为它是一个有效的数字字符，会被优先识别为数字常量的一部分，然后才是对象属 性访问运算符。

  ```js
  // 无效语法:
  42.toFixed( 3 ); // SyntaxError
  // 下面的语法都有效: 
  (42).toFixed( 3 ); // "42.000" 
  0.42.toFixed( 3 ); // "0.420" 
  42..toFixed( 3 ); // "42.000"
  42 .toFixed(3); // "42.000"(注意中间有个空格)
  ```

* 4、机器精度-误差范围值2^-52 (2.220446049250313e-16)

  ES6开始用Number.EPSILON表示

  ```js
  if (!Number.EPSILON) {
      Number.EPSILON = Math.pow(2,-52);
  }
  ```

  比较两个数字是否相等(在指定的误差范围内)

  ```js
  function numbersCloseEnoughToEqual(n1,n2) {
      return Math.abs( n1 - n2 ) < Number.EPSILON;
  }
  ```

* 5、能够被“安全”呈现的最大整数是2^53 - 1，ES6中Number.MAX_SAFE_INTEGER

  最 小 整 数 是 -9007199254740991， 在 ES6 中 被 定 义 为 Number. MIN_SAFE_INTEGER

* 检测一个值是否是整数(ES6）： Number.isInteger(a)
* 检测一个值是否是安全的整数（ES6）：Number.isSafeInteger(b)

* 操作中数字的安全范围就要小很多：Math.pow(-2,31)(-2147483648， 约-21 亿)到 Math.pow(2,31) - 1(2147483647，约 21 亿)

  a | 0可以将变量a中的数值转换为32位有符号整数；因为数位运算符|只适用于32位 整数(它只关心 32 位以内的值，其他的数位将被忽略)

* isNaN和Number.isNaN

  ```js
  var a = 2 / "foo";
  var b = "foo";
  a; // NaN
  b; "foo"
  window.isNaN( a ); // true window.isNaN( b ); // true——晕!
  ```

  ```js
  if (!Number.isNaN) {
    Number.isNaN = function(n) {
      return (
        typeof n === "number" &&
        window.isNaN( n )
  ); };
  }
  var a = 2 / "foo";
  var b = "foo";
  Number.isNaN( a ); // true Number.isNaN( b ); // false——好!
  ```

  

## 3.原生函数

* 常见的原生函数：

  > • String()
  > • Number()
  > • Boolean()
  > • Array()
  > • Object()
  > • Function()
  > • RegExp()
  > • Date()
  > • Error()
  > • Symbol()——ES6 中新加入的!

* 除非万不得已，否则尽量不要使用 `Object(..)`/`Function(..)`/`RegExp(..)`;

* 构造函数 Function 只在极少数情况下很有用，比如动态定义函数参数和函数体的时候;

* 强烈建议使用常量形式(如` /^a*b+/g`)来定义正则表达式，这样不仅语法简单，执行效率 也更高，因为 JavaScript 引擎在代码执行前会对它们进行预编译和缓存;

* `RegExp(..) `用在动态定义正则表达式

* `Data().now()`ES5

  ```js
  if (!Date.now) {
      Date.now = function(){
          return (new Date()).getTime();
      };
  }
  ```

* 原生原型

  ```js
  typeof Function.prototype; // function
  RegExp.prototype.toString(); // "/(?:)/"
  Array.isArray( Array.prototype ); // true
  ```

* 将原型作为默认值

  Function.prototype 是一个空函数，RegExp.prototype 是一个“空”的正则表达式(无 任何匹配)，而 Array.prototype 是一个空数组。对未赋值的变量来说，它们是很好的默 认值。

  ```js
  function isThisCool(
     vals = Array.prototype,
     fn = Function.prototype,
     rx = RegExp.prototype
  ) {
      return rx.test(
       vals.map( fn ).join( "" )
  		); 
  }
  ```

  

# 4.强制类型转换

## 4.1 类型转换

类型转换发生在静态类型语言的编译阶段，而强制类型转换则发生在动态类型语言的运行时(runtime)。

js中通常统称为强制类型转换：

* 隐式
* 显式

### 4.2.1 toString()

对普通对象来说，除非自行定义，否则 toString()(Object.prototype.toString())返回 内部属性 [[Class]] 的值，如 "[object Object]";

```js
Object.prototype.toString.call([]) // "[object Array]"
```

```js
var a = 1.07 * 1000 * 1000 * 1000 * 1000 * 1000 * 1000 * 1000;
a.toString(); // "1.07e21"

var a = [1,2,3];
a.toString(); // "1,2,3"
```

### 4.2.2 JSON 字符串化

JSON.stringify(..) 并不是强制类型转换;

所有安全的 JSON 值(JSON-safe)都可以使用 JSON.stringify(..) 字符串化；

`undefined`、`function`、`symbol (ES6+)`【忽略】和包含循环引用(对象之间相互引用，形成一个无限循环)【报错】的对象都不符合 JSON结构标准，支持 JSON 的语言无法处理它们

```js
JSON.stringify( undefined ); 		// undefined
JSON.stringify( function(){} ); // undefined
JSON.stringify([1,undefined,function(){},4]);		// "[1,null,null,4]"
JSON.stringify({ a:2, b:function(){} }) // "{\"a\":2}"
```



***

很多人误以为 toJSON() 返回的是 JSON 字符串化后的值，其实不然，除非我们确实想要对 字符串进行字符串化(通常不会!)。toJSON() 返回的应该是一个适当的值，可以是任何 类型，然后再由 JSON.stringify(..) 对其进行字符串化。

也就是说，toJSON() 应该“返回一个能够被字符串化的安全的 JSON 值”，而不是“返回 一个 JSON 字符串”。

* (1)字符串、数字、布尔值和 null 的 JSON.stringify(..) 规则与 ToString 基本相同。
*  (2) 如果传递给 JSON.stringify(..) 的对象中定义了 toJSON() 方法，那么该方法会在字符串化前调用，以便将对象转换为安全的 JSON 值。

### 4.2.3 ToNumber

```js
Number(false) // 0
Number(true)  // 1
Number(undefined) // NaN
Number(null) // 0
Number('') // 0
Number( [] ); // 0
Number( [ "abc" ] ); // NaN
```

ToNumber 对字符串的处理基本遵循数字常量的相关规则 / 语法

### 4.2.4 ToBoolean

JavaScript 中的值可以分为以下两类: 

* (1) 可以被强制类型转换为 false 的值

* (2) 其他(被强制类型转换为 true 的值)



以下这些是假值:

- undefined
- null
- false
- +0、-0 和 NaN
- ""

## 4.3 显式强制类型转换

### 4.3.1 字符串和数字转换

* `String(a)`

* `Number(b)`

* `a.toString()`

* `+c`

* 日期转换成数字

  ```js
  var d = new Date()
  +d // 1621417468385
  
  new Date().getTime() // 1621417468385
  // ES5方法
  Date.now()  // 1621417468385
  ```

  提倡使用 Date.now() 来获得当前的时间戳，使用 new Date(..).getTime() 来获得指定时间的时间戳

* `~`运算符

  位运算符只适于32位整数；这是通过抽象操作 ToInt32 来实现的；

  ToInt32 首先执行 ToNumber 强制类型转换，比如 "123" 会先被转换为 123，然后再执行 ToInt32

  **~ 返回 2 的补码**：~x 大致等同于 -(x+1)

  ```js
  ~ -1 //0
  
  var a = "Hello World";
  if (~a.indexOf( "lo" )) { // 找到匹配!
  }
  ```


* `~~`运算符:字位截除

  它对负数的处理与 Math. floor(..) 不同

  ~~ 中的第一个 ~ 执行 ToInt32 并反转字位，然后第二个 ~ 再进行一次字位反转，即将所有 字位反转回原值，最后得到的仍然是 ToInt32 的结果。

  ~~x 能将值截除为一个 32 位整数

  ```js
  Math.floor( -49.6 );    // -50
  ~~-49.6;                // -49
  
  Math.floor( 49.6 );    // 49
  ~~49.6;                // 49
  ```
## 4.4 隐式类型转换

### 4.4.2 字符串和数字

* `+` 操作符

  a + b

  如果a或b是字符串，那么执行拼接操作

  如果其中一个操作数是对象(包括数组)，则首先对其调用 ToPrimitive 抽象操作(规范 9.1 节)，该抽象操作再调用 [[DefaultValue]]，以数字作为上下文；

  ```js
  var a = {
    valueOf: function() { return 42; },
    toString: function() { return 4; }
  };
  a + "";         // "42"
  String( a );    // "4"
  ```

  a + ""会对a调用valueOf()方法，然后通过ToString抽象 操作将返回值转换为字符串。而 String(a) 则是直接调用 ToString()
  
  
  
  
  
  ```js
  [] + {} // "[object Object]"
  ```
  
  {} 出现在 + 运算符表达式中，因此它被当作一个值(空对象)来处理。第4 章讲过 [] 会被强制类型转换为 ""，而 {} 会被强制类型转换为 "[object Object]"
  
  ```js
  {} + [] // 0
  ```
  
  {} 被当作一个独立的空代码块(不执行任何操作)。代码块结尾不需 要分号，所以这里不存在语法上的问题。最后 + [] 将 [] 显式强制类型转换(参见第 4 章) 为 0。

 	

* `-`，`/`，`*`

  这三个都是用于数字的运算符，所以会先转为数字再做运算

​	

### 4.4.5 `||` 和`&&`

* || ：如果条件判断结果为 true 就返回第一个操作数的值，如果为 false 就返回第二个操作数的值

* &&：如果条件判断结果为 true 就返回第二个操作数的值，如果为 false 就返回第一个操作数的值

### 4.4.6 `symbol`的强制类型转换

符号不能够被强制类型转换为数字(显式和隐式都会产生错误)，但可以被强制类型转换 为布尔值(显式和隐式结果都是 true)

### 4.5 == 和 ===

== 允许在相等比较中进行强制类型转换，而 === 不允许。

```js
NaN == NaN // false
+0 === -0 // true
```

* 字符串和数字之间的宽松相等比较:

  将字符串的值转为数字去和另一数字比较

* 数字和布尔值之间的宽比较：

  将为布尔的值转成0或者1去和另一个数字比较

* `null`和`undefined`

  ```js
  null == undefined
  undefined == null
  ```

  ```js
  var a = null;
  var b;
  a == b;     // true
  a == null;  // true
  b == null;  // true
  a == false; // false
  b == false; // false
  a == ""; // false
  b == ""; // false
  a == 0;  // false
  b == 0;  // false
  ```

* 对象和非对象的转换

  将对象使用ToPrimitive转换去与另一个字符串/数字比较

  ```js
  var a = 'abc'
  var b = Object(a)
  
  a == b // true
  ```

  ```js
  var a = null;
  var b = Object( a ); // 和Object()一样
  a == b;  // false
  var c = undefined;
  var d = Object( c ); // 和Object()一样
  c == d; // false
  var e = NaN;
  var f = Object( e ); // 和new Number( e )一样
  e == f; // false
  ```

* 少见的情况

  ```js
  Number.prototype.valueOf = function() {
    return 3;
  };
  new Number( 2 ) == 3;   // true
  ```

  ```js
  var i = 2;
       Number.prototype.valueOf = function() {
           return i++;
  };
       var a = new Number( 42 );
       if (a == 2 && a == 3) {
           console.log( "Yep, this happened." );
  }
  ```

  ```js
  "0" == null;        // false
  "0" == undefined;   // false
  "0" == false;       // true ! (字符串与布尔比较，布尔转为数字)
  "0" == NaN;         // false  (字符串和数字比较，字符串转为数字，0和NaN不相等)
  "0" == 0;           // true
  "0" == "";          // false
  
  false == null;      // false
  false == undefined; // false
  false == NaN;       // false
  false == 0;         // true ! (字符串与布尔比较，布尔转为数字0)
  false == "";        // true ！(字符串与布尔比较，布尔转为数字0，数字和字符串比较，字符串''转为数字0)
  false == [];        // true ！(对象和布尔比较，布尔转为数字0，对象ToPrimitive([])为""，再转为数字0）
  false == {};        // false
  
  "" == null;         // false
  "" == undefined;    // false
  "" == NaN;          // false
  "" == 0;            // true ！
  "" == [];           // true ！
  "" == {};           // false
  
  0 == null;          // false
  0 == undefined;     // false
  0 == NaN;           // fasle
  0 == [];            // true ！
  0 == {};            // false
  ```

  ```js
  [] == ![] // true
  ```

  ```js
  2 == [2];       // true (数字和对象比较，数组ToPrimitive为'2',再转为数字2)
  "" == [null];   // true （[null]ToPrimitive为''）
  ```

  ```js
  0 == "\n"; //""、"\n"(或者 " " 等其他空格组合)等空字符串被 ToNumber 强制类型转换为 0
  ```

* 安全运用隐式强制类型转换

  - 如果两边的值中有 true 或者 false，千万不要使用 ==。
  - 如果两边的值中有 []、"" 或者 0，尽量不要使用 ==

  ### 4.6 抽象关系比较

  不要使用对象进行比较！

  记录个神奇的：

  ```js
  var a = { b: 42 };
  var b = { b: 43 };
  
  a < b;  // false
  a == b; // false
  a > b;  // false
  a <= b; // true  因为根据规范a <= b被处理为b < a，然后将结果反转。因为b < a的结果是false，所以 a <= b 的结果是 true。
  a >= b; // true
  ```

  

### 【ToPrimitive扩展】

> **应用场景：**在JavaScript中，如果想要将对象转换成基本类型时，也就是所谓的拆箱时，会调用toPrimitive()。
>
> **函数结构：**toPrimitive(input,preferedType?)
>
> **参数解释：**
>
> input是输入的值，即要转换的对象，必选；
>
> preferedType是期望转换的基本类型，他可以是字符串，也可以是数字。选填，默认为number；
>
> **执行过程：**
>
> 如果转换的类型是number，会执行以下步骤：
>
> 1. 如果input是原始值，直接返回这个值；
> 2.  否则，如果input是对象，调用input.valueOf()，如果结果是原始值，返回结果；
>
> 3. 否则，调用input.toString()。如果结果是原始值，返回结果；
>
> 4. 否则，抛出错误。
>
> 如果转换的类型是String，2和3会交换执行，即先执行toString()方法。
>
> 你也可以省略preferedType，此时，日期会被认为是字符串，而其他的值会被当做Number



# 5. 语法



## 5.1 语句和表达式

###  5.1.2 表达式的副作用

* ++ 

  ```js
  var a = 42;
  var b = a++;
  a;  // 43
  b;  // 42
  ```

  ++ 在前面时，++a，它的副作用(将 a 递增)产生在表达式返回结果值之前，而 a++ 的 副作用则产生在之后。

  ```js
  var a = 42, b;
  b = ( a++, a ); // 语句系列逗号运算符将多个独立的表达式语句串联成一个语句
  a;  // 43
  b;  // 43
  ```

  

### 5.1.3 上下文规则

* **标签语句**

  `foo:bar()`

  **带标签的循环 / 代码块十分少见，也不建议使用。**

  continue 和 break 语句都可以带 一个标签，因此能够像 goto 那样进行跳转;

  ```js
  console.log('=========continue==========');
  for (var i=0; i<4; i++) {
      for (var j=0; j<4; j++) {
          // 如果j和i相等，继续内层循环(没有标签的)
          if (j == i) {
              // 继续内层循环
              continue ;
          }
          // 跳过奇数结果
          if ((j * i) % 2 == 1) {
              // 继续内层循环(没有标签的)
              continue; 
          }
          console.log( i, j );
      }
  }
  // 得到：
  0 1
  0 2
  0 3
  1 0
  1 2
  2 0
  2 1
  2 3
  3 0
  3 2
  console.log('=========continue foo==========');
  foo:for (var i=0; i<4; i++) {
      for (var j=0; j<4; j++) {
          // 如果j和i相等，继续外层循环 
          if (j == i) {
              // 跳转到foo的下一个循环
              continue foo;
          }
          // 跳过奇数结果
          if ((j * i) % 2 == 1) {
              // 继续内层循环(没有标签的)
              continue; 
          }
          console.log( i, j );
      }
  }
  // 得到：
  1 0
  2 0
  2 1
  3 0
  3 2
  console.log('=========break==========');
  for (var i=0; i<4; i++) {
      for (var j=0; j<4; j++) {
          // 如果j和i相等，继续外层循环 
          if (j == i) {
              // 跳转到foo的下一个循环
              break ;
          }
          // 跳过奇数结果
          if ((j * i) % 2 == 1) {
              // 继续内层循环(没有标签的)
              continue; 
          }
          console.log( i, j );
      }
  }
  // 得到：
  1 0
  2 0
  2 1
  3 0
  3 2
  ```

  上述代码可以看出：此处的`continue foo`和`break`的作用一样，都是跳出本循环，到外层循环。`continue`是直接跳过此条件，继续内部的循环；

  ```js
  console.log('=========break==========');
  
  for (var i=0; i<4; i++) {
      for (var j=0; j<4; j++) {
          // 如果(i * j) >= 3，继续外层循环 
          if ((i * j) >= 3) {
              console.log( "stopping!", i, j );
              // 跳转到foo的下一个循环
              break; 
          }
          console.log( i, j );
      }
  }
  // 得到：
  0 0
  0 1
  0 2
  0 3
  1 0
  1 1
  1 2
  stopping! 1 3
  2 0
  2 1
  stopping! 2 2
  3 0
  stopping! 3 1
  
  console.log('==========break foo=========');
  
  foo:for (var i=0; i<4; i++) {
      for (var j=0; j<4; j++) {
          if ((i * j) >= 3) {
              console.log( "stopping!", i, j );
              // 跳出标签 foo 所在的循环 / 代码块，继续执行后面的代码
              break foo; 
          }
          console.log( i, j );
      }
  }
  // 得到：
  0 0
  0 1
  0 2
  0 3
  1 0
  1 1
  1 2
  stopping! 1 3
  ```

  上述可以看出，`break foo`是跳出标签所在的循环/代码块，继续后面的代码。不带标签的`break`是跳出内部循环，执行外部循环；👍

  ```js
  // 一般的代码块也可以用标签，可用break
  function foo() {
      bar: {
          console.log( "Hello" );
          break bar;
          console.log( "never runs" );
      }
      console.log( "World" );
  }
  foo()
  // 得到：
  Hello
  World
  ```

  

* `else if`

  事实上`javascript`没有`else if`，但 if 和 else 只包含单条语句的时候可以省略代码块的 { }；

  else 中是一个单独的 if 语句；

  else if极为常见，能省掉一层代码缩进，所以很受青睐。但这只是我们自己发明的用法，

  切勿想当然地认为这些都属于 JavaScript 语法的范畴

## 5.2 运算符优先级

* 用`,`来连接一系列语句的时候，它的优先级最低
* `&&`比`=`的优先级高
* `&&`比`||`的优先级高，`||`比`?:`的优先级高
* 多个相同优先级的运算符同时出现时，需要注意运算符的关联：
  * `&&`和`||`都是左关联，`a && b && c`会被处理成`(a && b) && c`
  * `?:`是右关联，`a ? b : c ? d : e`会被处理成`a ? b : (c ? d : e)`
  * `=`是右关联，`a = b = c = 42`会被处理成`a = (b = (c = 42))`

## 5.3 自动分号插入（ASI）

## 5.4 错误

JavaScript 中有很多错误类型，分为两大类:早期错误(编译时错误，无法被捕获)和运 行时错误(可以通过 try..catch 来捕获)。所有语法错误都是早期错误，程序有语法错误 则无法运行；

**暂时性死区**（TDZ）：指的是由于代码中的变量还没有初始化而不能被引用的情况（ES6）；

```js
{
   a = 2;      // ReferenceError!
   let a;
}
```

## 5.6 try..finally

finally 中的代码总是会在 try 之后执行，如果有 catch 的话则在 catch 之后执行。也可以将 finally 中的代码看作一个回调函数，即无论出现什么情况最后一定会被调用。

`try...finally`放在函数里面时，`try`中有return或者抛出错误，执行函数时，依然会先执行finally中的内容，如果finally中有return或者抛出错误的话，就会直接先return finally中，就不会再走try中了；