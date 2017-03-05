
React可以使用React.createClass、ES6 classes、纯函数3种方式构建组件。使用React.createClass会自动绑定每个方法的this到当前组件，但使用ES6 classes或纯函数时，就要靠手动绑定this了。接下来介绍React中三种绑定this的方法

## bind()

`Function.prototype.bind(thisArg [, arg1 [, arg2, …]])`是ES5新增的函数扩展方法，bind()返回一个新的函数对象，该函数的this被绑定到thisArg上，并向事件处理器中传入参数

```
import React, {Component} from 'react'

class Test extends React.Component {
	constructor (props) {
	    super(props)
		this.state = {message: 'Allo!'}
	}

	handleClick (name, e) {
		console.log(this.state.message + name)
	}

	render () {
		return (
			<div>
				<button onClick={ this.handleClick.bind(this, '赵四') }>Say Hello</button>
			</div>
		)
	}
}
```

要注意的是，跟在this（或其他对象）后面的参数，之后它们会被插入到目标函数的参数列表的开始位置，传递给绑定函数的参数会跟在它们的后面。

## 构造函数内绑定

在构造函数`constructor`内绑定this，好处是仅需要绑定一次，避免每次渲染时都要重新绑定，函数在别处复用时也无需再次绑定

```
import React, {Component} from 'react'

class Test extends React.Component {
	constructor (props) {
	    super(props)
		this.state = {message: 'Allo!'}
		this.handleClick = this.handleClick.bind(this)
	}

	handleClick (e) {
		console.log(this.state.message)
	}

	render () {
		return (
			<div>
				<button onClick={ this.handleClick }>Say Hello</button>
			</div>
		)
	}
}
```

## 箭头函数

箭头函数则会捕获其所在上下文的this值，作为自己的this值，使用箭头函数就不用担心函数内的this不是指向组件内部了。可以按下面这种方式使用箭头函数：

```
class Test extends React.Component {
	constructor (props) {
	    super(props)
		this.state = {message: 'Allo!'}
	}

	handleClick (e) {
		console.log(this.state.message)
	}

	render () {
		return (
			<div>
				<button onClick={ ()=>{ this.handleClick() } }>Say Hello</button>
			</div>
		)
	}
}
```
这种方式有个小问题，因为箭头函数总是匿名的，如果你打算移除监听事件，可以改用以下方式：
```
class Test extends React.Component {
	constructor (props) {
	    super(props)
		this.state = {message: 'Allo!'}
	}

	handleClick = (e) => {
		console.log(this.state.message)
	}

	render () {
		return (
			<div>
				<button onClick={ this.handleClick }>Say Hello</button>
			</div>
		)
	}
}
```

不过，在Classes中直接赋值是ES7的写法，ES6并不支持，只用ES6的话可以用下面写法：

```
class Test extends React.Component {
	constructor (props) {
	    super(props)
		this.state = {message: 'Allo!'}
		this.handleClick = (e) => {
			console.log(this.state.message)
		}
	}

	render () {
		return (
			<div>
				<button onClick={ this.handleClick }>Say Hello</button>
			</div>
		)
	}
}
```

三种方法都能实现this的绑定，至于用哪种方式还跟着自己的习惯来。

[更多干货》》](http://4bin.cn/blog/)



## 参考

- [MDN文档 Function.prototype.bind()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)
- [MDN稳定 Arrow Functions](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

