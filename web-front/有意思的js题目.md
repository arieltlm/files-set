# 有意思的js题目

[toc]

# 1.按下标插入


现在有一个数组存放字符串数据：

`['item1', 'item2', 'item3', 'item4', 'item5']`
有另外一个数组存放一组对象：

`[
  { content: 'section1', index: 0 },
  { content: 'section2', index: 2 }
]`
它每个对象表示的是会往原来的数组的 index 坐标插入 content 数据（index 不会重复）：

	       0      1      2      3      4
	     item1  itme2  item3  item4  item5
	    ^             ^ 
	    |             |
	 section1     section2  
 
最后结果是：`['section1', 'item1', 'item2', 'section2', 'item3', 'item4', 'item5']`
请你完成 injectSections 函数，可以达到上述的功能：

```js
injectSections(
  ['item1', 'item2', 'item3', 'item4', 'item5'],
  [
    { content: 'section1', index: 0 },
    { content: 'section2', index: 2 }
  ]
) // => ['section1', 'item1', 'item2', 'section2', 'item3', 'item4', 'item5']
```

**解答**：

* 思路一：
复制items，根据index在原items中位置得到此位置的元素，然后在新数组中找到此元素的index，再利用splice插入。

```js
const injectSections = (items, sections) => {
	let temp = [...items];
	sections.forEach((item, j) => {
		const cons = item.index;
		let k = temp.findIndex((val,i) => items[cons] == val)
		if(k == 0){
			temp.unshift(item.content)
		} else {
			temp.splice(k,0,item.content)
		}
	})
	console.log(temp)
	return temp;
}
```
* 思路二：
倒叙，从最大的到最小的插入；

```js
const injectSections = (items, sections) => {
	sections.sort((a, b) => b.index - a.index)
	sections.forEach((item, index) => {
		items.splice(item.index, 0, item.content)
	})
	return items;
}
```

* 思路三：

将sections中index相继加起来，比如第一个即为0，第二个就为0+2=2；然后插入

```js

```

# 2.let的变量除了作用域是在for区块中，而且会为每次循环执行建立新的词法环境(LexicalEnvironment)

```js
function getName(){
    for(let i=0;i<5;i++){
        setTimeout(function(){
            console.log(i)
        },i*1000)
    }
    return 
    {
        name:'京城一灯'
    }
}
console.log(getName());
```

**答案：**undefined 0 1 2 3 4

**解析：**

* 1.第一个点：undefined，这是因为return后换行了,JavaScirpt会在return语句后自动插入了分号。

**分号自动添加的情况：**

* （1）如果下一行的开始与本行的结尾连在一起解释，JavaScript就不会自动添加分号；  
* （2）只有下一行的开始于本行的结尾无法放在一起解释，JavaScript引擎才会自动添加分号；  
* （3）如果一行的起首是++或--运算符，则他们后面自动添加分号；  
* （4）如果continue、break、return、throw这四个语句后面，直接跟换行符，则会自动添加分号。 

* 2.第二个点：let的变量除了作用域是在for区块中，而且会为每次循环执行建立新的词法环境(LexicalEnvironment)，  因为用let声明的，所以每个i一个作用域。这里如果是用var声明，则最后输出的都是5。

# 3.变量声明问题：

**IIFE的函数**:

```js
var b = 10;
(function b() {
   // 内部作用域，会先去查找是有已有变量b的声明，有就直接赋值20，确实有了呀。发现了具名函数 function b(){}，拿此b做赋值；
   // IIFE的函数无法进行赋值（内部机制，类似const定义的常量），所以无效。
  // （这里说的“内部机制”，想搞清楚，需要去查阅一些资料，弄明白IIFE在JS引擎的工作方式，堆栈存储IIFE的方式等）
    b = 20;
    console.log(b); // [Function b]
    console.log(window.b); // 10，不是20
})();
```

**严格模式**：

```js
var b = 10;
(function b() {
  'use strict'
  b = 20;
  console.log(b)
})() // "Uncaught TypeError: Assignment to constant variable."
```


**`window`**:

```js
var b = 10;
(function b() {
    window.b = 20; 
    console.log(b); // [Function b]
    console.log(window.b); // 20是必然的
})();
```
**`var`**:

```js
var b = 10;
(function b() {
    var b = 20; // IIFE内部变量
    console.log(b); // 20
   console.log(window.b); // 10 
})();
```




















