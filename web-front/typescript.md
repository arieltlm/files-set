# 1.编译
TypeScript 编译的时候即使报错了，还是会生成编译结果，我们仍然可以使用这个编译之后的文件。
如果要在报错的时候终止 js 文件的生成，可以在 tsconfig.json 中配置 noEmitOnError 即可
# 2.原始数据类型
JavaScript 的类型分为两种：原始数据类型（Primitive data types）和对象类型（Object types）

原始数据类型包括：布尔值、数值、字符串、null、undefined 以及 ES6 中的新类型 Symbol

**ts中的原始数据类型**————布尔值、数值、字符串、空值、Null 和 Undefined

## 1).布尔值

```js
let createdByNewBoolean: boolean = new Boolean(1); // 报错
let createdByNewBoolean1: Boolean = new Boolean(1);
let createdByNewBoolean2: Boolean = Boolean(1);
```

`new Boolean `或者`Boolean `返回的是一个 `Boolean` 对象，不属于原始类型`boolean`


在 TypeScript 中，`boolean` 是 JavaScript 中的基本类型，而 `Boolean` 是 JavaScript 中的构造函数。

## 2).数值number

```js
// ES6 中的二进制表示法
let binaryLiteral: number = 0b1010;
// ES6 中的八进制表示法
let octalLiteral: number = 0o744;
```

编译后：称为十进制的数据

```js
// ES6 中的二进制表示法
var binaryLiteral = 10;
// ES6 中的八进制表示法
var octalLiteral = 484;
```

## 3).字符串


## 4).空值
在 TypeScript 中，可以用 void 表示没有任何返回值的函数

声明一个 void 类型的变量没有什么用，因为你只能将它赋值为 undefined 和 null

## 5).null和undefined
与 void 的区别是，undefined 和 null 是所有类型的子类型

# 3.任意值

普通类型的不允许改变类型；`any` 类型，则允许被赋值为任意类型

在任意值上访问任何属性都是允许的,也允许调用任何方法;


```js
let anyThing: any = 'hello';
console.log(anyThing.myName);
console.log(anyThing.myName.firstName);
anyThing.setName('Jerry').sayHello();
```

上述编译不报错

**声明一个变量为任意值之后，对它的任何操作，返回的内容的类型都是任意值；**
**变量如果在声明的时候，未指定其类型，那么它会被识别为任意值类型；**

# 4.类型推论

定义的时候，做了赋值操作，这时候如果没有定义类型，ts就会根据赋的值的类型推论出这个变量的类型，就认定了它。

如果定义的时候，没有做赋值操作，就会被推断为`any`，后续就不做类型检查。

# 5.联合类型

可以一次定义多个类型，用|来表示

```js
let myFavoriteNumber: string | number;
```

**只能访问此联合类型的所有类型里共有的属性或方法**

```js
// 报错
function getLength(something: string | number): number {
    return something.length;
}
// 正确
function getString(something: string | number): string {
    return something.toString();
}
```

`number`没有length属性，所以报错；number和string都有toString()方法所以不报错。

联合类型在赋值的时候，会根据值来推断它是哪个类型，从而决定某些属性或方法可用否。

# 6.对象的类型-接口（interfaces)
* 接口（`interfaces`）是对行为的抽象；

* 类（`class`）实现具体如果行动

```js
interface Person {
    name: string;
    readonly age: number;
    gender?: string;
}

let tom: Person = {
    name: 'Tom',
    age: 25
};

tom.name="Jack" // 正确
tom.age = 30 // 报错，只读不允许修改
```

**赋值的时候，变量的形状必须和接口的形状保持一致。**属性不能多也不能少
**？**可以使其成为可选的，即可有可无。


`[propName: string]: any;`可以添加这句添加任意属性。但是一旦添加此句，**其他的属性类型必须这个定义类型的子集**。比如`[propName: string]: string;`对于`age:25`来说就是报错的，如果写成`[propName: string]: string|number;`就可以了


**只读`readonly`的约束存在于第一次给对象赋值的时候，而不是第一次给只读属性赋值的时候**;

# 7.数组

## 1).[类型+方括号]:

```js
let fibonacci: number[] = [1, 1, 2, 3, 5]; // 限制其中元素只能是数字
let list: any[] = ['xcatliu', 25, { website: 'http://xcatliu.com' }]; //any比较常用
```
## 2).数组范型

```js
let fibonacci: Array<number> = [1, 1, 2, 3, 5];
```

## 3).接口表示数组
一般不会这么做，因为这种方式比前两种方式复杂多了

## 4).类数组

```js
function sum() {
    let args: {
        [index: number]: number;
        length: number;
        callee: Function;
    } = arguments;
}
```


常用的类数组都有自己的接口定义,如 IArguments, NodeList, HTMLCollection;TypeScript 中定义好了的类型；

```js
interface IArguments {
    [index: number]: any;
    length: number;
    callee: Function;
}

function sum() {
    let args: IArguments = arguments;
}
```






