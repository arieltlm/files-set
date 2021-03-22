[toc]

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

  