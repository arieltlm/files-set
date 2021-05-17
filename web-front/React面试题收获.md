# React面试题收获

--2018年5月

# 1、React.PureComponent

React有个生命周期为shouldComponentUpdate，而React.Component和React.PureComponent的区别正在这个生命周期函数上。

继承React.Component在state或props发生变化时就会render，此变化不是说值或者地址的变化，是只要设置它就会render。比如原state中有`flag:true`,执行`this.setState({flag: true})`的时候，就会触发render；

继承React.PureComponent对于上述情况不会触发render。主要原因是React.PureComponent在shouldComponentUpdate函数中做了处理，进行了浅比较。对于简单数据类型，如果之前的值和之后的值一致，那就不做render。对于引用数据类型，只要引用地址不变，就不会render。这一点————

* 好处：提高性能；
* 坏处——state或props中存在深层次的对象的话，深层次发生变化，检测不到，无法render。
	**对于这一坏处——解决办法：**
	+ 1、在深层数据结构发生变化时调用 forceUpdate() 来确保组件被正确地更新；
	+ 2、使用 immutable 对象加速嵌套数据的比较。

另外——React.PureComponent 中的 `shouldComponentUpdate()` 将跳过所有子组件树的 prop 更新。

再一点，React.PureComponent中不能重写`shouldComponentUpdate()`,否则会报错；

```js
class ListOfWords extends React.PureComponent {
 render() {
     return <div>{this.props.words.join(',')}</div>;
 }
}
class WordAdder extends React.Component {
 constructor(props) {
     super(props);
     this.state = {
         words: ['marklar']
     };
     this.handleClick = this.handleClick.bind(this);
 }
 handleClick() {
     // This section is bad style and causes a bug
     const words = this.state.words;
     words.push('marklar');
     this.setState({words: words});
 }
 render() {
     return (
         <div>
             <button onClick={this.handleClick}>click</button>
             <ListOfWords words={this.state.words} />
         </div>
     );
 }
}
```
上面代码中，无论你怎么点击按钮，ListOfWords渲染的结果始终没变化，原因就是WordAdder的word的引用地址始终是同一个。---引用[React.Component 与 React.PureComponent（React之性能优化）](https://www.cnblogs.com/clover77/p/9394514.html)

# 2、如何在 JSX 回调中绑定方法或事件处理程序?
————再记录一遍

实现这一点有三种可能的方法：

* Binding in Constructor: 在 JavaScript 类中，方法默认不被绑定。这也适用于定义为类方法的 React 事件处理程序。通常我们在构造函数中绑定它们。

```js
class Component extends React.Componenet {
  constructor(props) {
    super(props)
    this.handleClick = this.handleClick.bind(this)
  }

  handleClick() {
    // ...
  }
}
```
* Public class fields syntax: 如果你不喜欢 bind 方案，则可以使用 public class fields syntax 正确绑定回调。

```js
handleClick = () => {
  console.log('this is:', this)
}
<button onClick={this.handleClick}>
  {'Click me'}
</button>
```
* Arrow functions in callbacks: 你可以在回调函数中直接使用 arrow functions。

```js
<button onClick={(event) => this.handleClick(event)}>
  {'Click me'}
</button>
```
注意： 如果回调函数作为属性传给子组件，那么这些组件可能触发一个额外的重新渲染。在这些情况下，考虑到性能，最好使用 .bind() 或 public class fields syntax 方案（ES6方法)。

# 3、refs
## 下面是几个适合使用 refs 的情况：

* 管理焦点，文本选择或媒体播放。
* 触发强制动画。
* 集成第三方 DOM 库

***
## 创建refs有两种方式：

* 1、**（此只适用于v16.3以上的版本）**
 Refs 是使用 React.createRef() 方法创建的，并通过 ref 属性添加到 React 元素上。为了在整个组件中使用refs，只需将 ref 分配给构造函数中的实例属性。

```js
class MyComponent extends React.Component {
  constructor(props) {
    super(props)
    this.myRef = React.createRef()
  }
  render() {
    return <div ref={this.myRef} />
  }
}
```
获取节点`const node = this.myRef.current;`

ref 的值根据节点的类型而有所不同：

* 当 ref 属性用于 HTML 元素时，构造函数中使用 React.createRef() 创建的 ref 接收底层 DOM 元素作为其 current 属性。
* 当 ref 属性用于自定义 class 组件时，ref 对象接收组件的挂载实例作为其 current 属性。
* 你不能在函数组件上使用 ref 属性，因为他们没有实例。

**为 class 组件添加 Ref:**

```js
class AutoFocusTextInput extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();
  }

  componentDidMount() {
    this.textInput.current.focusTextInput();
  }

  render() {
    return (
      <CustomTextInput ref={this.textInput} />
    );
  }
}
// CustomTextInput必须是class组件
class CustomTextInput extends React.Component {
  // ...
}
```

* 2、(此不用考虑react版本）回调函数

```js
class SearchBar extends Component {
   constructor(props) {
      super(props);
      this.txtSearch = null;
      this.state = { term: '' };
      this.setInputSearchRef = e => {
         this.txtSearch = e;
      }
   }

   onInputChange(event) {
      this.setState({ term: this.txtSearch.value });
   }

   render() {
      return (
         <input
            value={this.state.term}
            onChange={this.onInputChange.bind(this)}
            ref={this.setInputSearchRef} />
      );
   }
}
```

即通常写的：

```js
...
render() {
  return (
     <input ref={(inputRef) => this.inputRef = inputRef} />
  );
}
...

```
[此处说](https://github.com/semlinker/reactjs-interview-questions#%E5%A6%82%E4%BD%95%E5%B0%86%E5%8F%82%E6%95%B0%E4%BC%A0%E9%80%92%E7%BB%99%E4%BA%8B%E4%BB%B6%E5%A4%84%E7%90%86%E7%A8%8B%E5%BA%8F%E6%88%96%E5%9B%9E%E8%B0%83%E5%87%BD%E6%95%B0)-此法不推荐【注意： 你也可以使用内联引用回调，尽管这不是推荐的方法。】

***
## 将 DOM Refs 暴露给父组件

* 如果你使用 16.3 或更高版本的 React, 这种情况下我们推荐使用 [ref 转发](https://zh-hans.reactjs.org/docs/forwarding-refs.html)。Ref 转发使组件可以像暴露自己的 ref 一样暴露子组件的 ref。

```js
// Ref forwarding 是一个特性，它允许一些组件获取接收到 ref 对象并将它进一步传递给子组件。

const ButtonElement = React.forwardRef((props, ref) => (
  <button ref={ref} className="CustomButton">
    {props.children}
  </button>
));

// Create ref to the DOM button:
const ref = React.createRef();
<ButtonElement ref={ref}>{'Forward Ref'}</ButtonElement>
```

* 如果你使用 16.2 或更低版本的 React，或者你需要比 ref 转发更高的灵活性，你可以使用[这个替代方案](https://gist.github.com/gaearon/1a018a023347fe1c2476073330cc5509)将 ref 作为特殊名字的 prop 直接传递。

# 4. 构造函数使用带 props 参数的目的是什么?
在调用super()方法之前，子类构造函数不能使用this引用。这同样适用于ES6子类。将props参数传递给super()的主要原因是为了在子构造函数中访问this.props。

* 带 props 参数:

```js
class MyComponent extends React.Component {
  constructor(props) {
    super(props)

    console.log(this.props) // prints { name: 'John', age: 42 }
  }
}
```

* 不带 props 参数:

```js
class MyComponent extends React.Component {
  constructor(props) {
    super()

    console.log(this.props) // prints undefined

    // but props parameter is still available
    console.log(props) // prints { name: 'John', age: 42 }
  }

  render() {
    // no difference outside constructor
    console.log(this.props) // prints { name: 'John', age: 42 }
  }
}
```
上面的代码片段显示this.props仅在构造函数中有所不同。 它在构造函数之外是相同的。

# 5.classname

[https://github.com/JedWatson/classnames](https://github.com/JedWatson/classnames)

```bash
npm install classnames
```

```js
classNames('foo', 'bar'); // => 'foo bar'
classNames('foo', { bar: true }); // => 'foo bar'
classNames({ 'foo-bar': true }); // => 'foo-bar'
classNames({ 'foo-bar': false }); // => ''
classNames({ foo: true }, { bar: true }); // => 'foo bar'
classNames({ foo: true, bar: true }); // => 'foo bar'

// lots of arguments of various types
classNames('foo', { bar: true, duck: false }, 'baz', { quux: true }); // => 'foo bar baz quux'

// other falsy values are just ignored
classNames(null, false, 'bar', undefined, 0, 1, { baz: null }, ''); // => 'bar 1'

var arr = ['b', { c: true, d: false }];
classNames('a', arr); // => 'a b c'

let buttonType = 'primary';
classNames({ [`btn-${buttonType}`]: true });
```

# 6. componentWillMount()和componentDidMount() 

建议避免在 componentWillMount() 生命周期方法中执行异步初始化。在 mounting 发生之前会立即调用 componentWillMount()，且它在 render() 之前被调用，因此在此方法中更新状态将不会触发重新渲染。应避免在此方法中引入任何副作用或订阅操作。我们需要确保对组件初始化的异步调用发生在 componentDidMount() 中，而不是在 componentWillMount() 中。

```js
componentDidMount() {
  axios.get(`api/todos`)
    .then((result) => {
      this.setState({
        messages: [...result.data]
      })
    })
}
```
# 7. 为何要在constrctor中写super(props)

[为什么我们要写 super(props) ？](https://overreacted.io/zh-hans/why-do-we-write-super-props/)

* 问：为何要在constrctor中写super()?

	答：在 JavaScript 中，super 指的是父类（即超类）的构造函数。（在我们的例子中，它指向了 React.Component 的实现。）值得注意的是，在调用父类的构造函数之前，你是不能在 constructor 中使用 this 关键字的。JavaScript 不允许这个行为。
	
	```js
	class Checkbox extends React.Component {
	  constructor(props) {
	    // 🔴  还不能使用 `this`
	    super(props);
	    // ✅  现在可以了
	    this.state = { isOn: true };
	  }
	  // ...
	}
	```
* 问： 为何要使用super(props）要传入props？
	
	答：为了让 React.Component 构造函数能够初始化 this.props，将 props 传入 super 是必须的；可是我们也看到假如在constrcor中不写super(props）的时候，在render 函数或其他方法中仍然可以使用this.props。事实证明，React 在调用构造函数后也立即将 props 赋值到了实例上：
	
	```js
	// React 内部
  	const instance = new YourComponent(props);
  	instance.props = props;
	```
	因此即便你忘记了将 props 传给 super()，React 也仍然会在之后将它定义到实例上。这么做是有原因的。当 React 增加了对类的支持时，不仅仅是为了服务于 ES6。其目标是尽可能广泛地支持类抽象。因而 React 刻意地没有显式要求调用 super() —— 即便 ES6 自身就包含这个机制。所有最好写上super(props）,以避免在构造函数中使用this.props等一些方法调用产生错误。

# 8. 在当前项目中获取react的版本

```js
console.log(React.version)
```

