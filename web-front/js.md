# 1.let和const变量提升

```js
let name = 'ConardLi'
{
  console.log(name) // Uncaught ReferenceError: name is not defined
  let name = 'code秘密花园'
}
```

**变量的赋值可以分为三个阶段：**

* 创建变量，在内存中开辟空间
* 初始化变量，将变量初始化为undefined
* 真正赋值

**关于let、var和function：**

* let 的「创建」过程被提升了，但是初始化没有提升。
* var 的「创建」和「初始化」都被提升了。
* function 的「创建」「初始化」和「赋值」都被提升了。

# 2.let和var与循环异步

```
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 1);
}

for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 1);
}
```

输出结果为：3 3 3 and 0 1 2

**分析：**
由于JavaScript中的事件执行机制，setTimeout函数真正被执行时，循环已经走完。 由于第一个循环中的变量i是使用var关键字声明的，因此该值是全局的。 在循环期间，我们每次使用一元运算符++都会将i的值增加1。 因此在第一个例子中，当调用setTimeout函数时，i已经被赋值为3。

在第二个循环中，使用let关键字声明变量i：使用let（和const）关键字声明的变量是具有块作用域的（块是{}之间的任何东西）。 在每次迭代期间，i将被创建为一个新值，并且每个值都会存在于循环内的块级作用域。

# 3.箭头函数的this指向

```js
const shape = {
  radius: 10,
  diameter() {
    return this.radius * 2;
  },
  perimeter: () => 2 * Math.PI * this.radius
};

shape.diameter(); // 20
shape.perimeter(); // NaN
```

**分析**：diameter是普通函数，而perimeter是箭头函数。

对于箭头函数，this关键字指向是它所在上下文（定义时的位置）的环境，与普通函数不同！ 这意味着当我们调用perimeter时，它不是指向shape对象，而是指其定义时的环境（window）。没有值radius属性，返回undefined。

# 4. new Number()

```
let a = 3;
let b = new Number(3);

console.log(b == c); // true
console.log(b === c); // false
```

**解析：**
new Number（）是一个内置的函数构造函数。 虽然它看起来像一个数字，但它并不是一个真正的数字：它有一堆额外的功能，是一个对象。
==会引发隐式类型转换，右侧的对象类型会自动拆箱为Number类型。
使用===操作符时，类型和值都需要相等

# 5.静态方法

```js
class Chameleon {
  static colorChange(newColor) {
    this.newColor = newColor;
  }

  constructor({ newColor = "green" } = {}) {
    this.newColor = newColor;
  }
}

const freddie = new Chameleon({ newColor: "purple" });
freddie.colorChange("orange"); // Uncaught TypeError: freddie.colorChange is not a function
```
**解析：**
colorChange方法是静态的。 静态方法仅在创建它们的构造函数中存在，并且不能传递给任何子级。 由于freddie是一个子级对象，函数不会传递，所以在freddie实例上不存在freddie方法：抛出TypeError。

# 6.函数也是对象
当我们这样做时会发生什么? -----Nothing, this is totally fine!

```js
function bark() {
  console.log("Woof!");
}

bark.animal = "dog";
```

**解析：**
这在JavaScript中是可能的，因为函数也是对象！（原始类型之外的所有东西都是对象）

函数是一种特殊类型的对象。您自己编写的代码并不是实际的函数。 该函数是具有属性的对象，此属性是可调用的。

# 7.函数改变变量

```js
var type = 'images';
var size = {width: 800, height: 600};
var format = ['jpg', 'png'];

function change(type, size, format){
    type = 'video';
    size = {width: 1024, height: 768};
    format.push('map');
}

change(type, size, format);

console.log(type, size, format); //'images'  {width: 800, height: 600}. ['jpg', 'png', 'map']
```

解析：
实际上就是函数形参的传递根据实参值的类型分为值复制（对应基本类型）和引用复制（对应引用类型）；

通过值复制的值无论怎么改变都不会影响原来的值（对应type变量的情况），而通过引用复制的值改变后原值也会改变（对应format变量的情况），但是**直接改变引用本身并不会影响其他引用**（即引用另一个引用类型的值时，原值的其他引用不会改变；对应size变量的情况）；

# 8. 不使用new 给构造函数传值

```js
function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}

const lydia = new Person("Lydia", "Hallie");
const sarah = Person("Sarah", "Smith");

console.log(lydia); // Person {firstName: "Lydia", lastName: "Hallie"}
console.log(sarah); // undefined

console.log(window.firstName); // Sarah
console.log(window.lastName); // Smith
```

**解析：**
对于sarah，我们没有使用new关键字。 使用new时，它指的是我们创建的新空对象。 但是，如果你不添加new它指的是全局对象！

我们指定了this.firstName等于'Sarah和this.lastName等于Smith。 我们实际做的是定义global.firstName ='Sarah'和global.lastName ='Smith。 sarah本身的返回值是undefined

# 9.事件传播的三个阶段
捕获 > 目标 > 冒泡

在捕获阶段，事件通过父元素向下传递到目标元素。 然后它到达目标元素，冒泡开始。

# 10. 除基础对象外，所有对象都有原型


除基础对象外，所有对象都有原型。 基础对象可以访问某些方法和属性，例如.toString。 这就是您可以使用内置JavaScript方法的原因！ 所有这些方法都可以在原型上找到。 虽然JavaScript无法直接在您的对象上找到它，但它会沿着原型链向下寻找并在那里找到它，这使您可以访问它。

译者注：基础对象指原型链终点的对象。基础对象的原型是null。

# 11. 前++，后++

```js
let number = 0;
console.log(number++); // 0
console.log(++number); // 2
console.log(number); // 2
```

**解析：**
**后缀一元运算符++：**

>1.返回值（返回0）

>2.增加值（数字现在是1）

**前缀一元运算符++：**

>1.增加值（数字现在是2）

>2.返回值（返回2）


# 12.标记的模板字符串

```
function getPersonInfo(one, two, three) {
  console.log(one); // (3) ["", " is ", " years old", raw: Array(3)]
  console.log(two); // Lydia
  console.log(three); // 21
}

const person = "Lydia";
const age = 21;

getPersonInfo`${person} is ${age} years old`;
```

**解析：**
如果使用标记的模板字符串，则第一个参数的值始终是字符串值的数组。 其余参数获取传递到模板字符串中的表达式的值！

# 13. 对象比较

```js
function checkAge(data) {
  if (data === { age: 18 }) {
    console.log("You are an adult!");
  } else if (data == { age: 18 }) {
    console.log("You are still an adult.");
  } else {
    console.log(`Hmm.. You don't have an age I guess`);
  }
}

checkAge({ age: 18 }); // Hmm.. You don't have an age I guess
```

**解析：**
在比较相等性，原始类型通过它们的值进行比较，而对象通过它们的引用进行比较。JavaScript检查对象是否具有对内存中相同位置的引用。

我们作为参数传递的对象和我们用于检查相等性的对象在内存中位于不同位置，所以它们的引用是不同的。

这就是为什么`{ age: 18 } === { age: 18 }`和 `{ age: 18 } == { age: 18 }` 返回 false的原因。

# 14.扩展运算符（... args）返回一个带参数的数组

```js
function getAge(...args) {
  console.log(typeof args); // object
}

getAge(21);
```
**解析：**
扩展运算符（... args）返回一个带参数的数组。 数组是一个对象，因此typeof args返回object。