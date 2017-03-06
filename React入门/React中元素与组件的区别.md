
在初学 React 的时候，分不清 React 组件和 React 元素，着实踩了一些坑。搞清楚 React 中什么是组件，什么是元素，既可以理清楚概念，也可以让你避免一些不必要的错误。

## React 元素

React 元素（React element），它是 React 中最小基本单位，我们可以使用 JSX 语法轻松地创建一个 React 元素:

```
const element = <div>I'm element</div>
```

React 元素不是真实的 DOM 元素，它仅仅是 js 的普通对象（plain objects），所以也没办法直接调用 DOM 原生的 API，这个对象大概是这样的：

```
{
	_context: Object,
	_owner: null,
	key: null,
	props: object,
	ref: null,
	type: "div"
}
```

只有在这个元素渲染被完成后，才能通过选择器的方式获取它对应的 DOM 元素。不过，按照 React 有限状态机的设计思想，应该使用状态和属性来表述组件，要尽量避免 DOM 操作，即便要进行 DOM 操作，也应该使用 React 提供的接口`ref`和`getDOMNode()`。一般使用 React 提供的接口就足以应付需要 DOM 操作的场景了，因此像 jQuery 强大的选择器在 React 中几乎没有用武之地了。 

除了使用 JSX 语法，我们还可以使用 `React.createElement()` 和 `React.cloneElement()` 来构建 React 元素。

###  React.createElement()

JSX 语法就是用`React.createElement()`来构建 React 元素的。它接受三个参数，第一个参数可以是一个标签名。如`div`、`span`，或者 React 组件。第二个参数为传入的属性。第三个以及之后的参数，皆作为组件的子组件。

```
React.createElement(
    type,
    [props],
    [...children]
)
```

### React.cloneElement()

`React.cloneElement()`与`React.createElement()`相似，不同的是它传入的第一个参数是一个 React 元素，而不是标签名或组件。

```
React.cloneElement(
  element,
  [props],
  [...children]
)
```

## 元素与组件的区别

组件是由元素构成的。元素数据结构是普通对象，而组件数据结构是类或纯函数。除此之外，还有几点区别要注意：

### JSX 语法中的区别

JSX 语法中，

## 最后

最后，打个不恰当的比喻，React 组件是`MyComponent`，React 元素就是`<MyComponent />`。




React数据类型分为 React element 和 React class，React element 是构建 React 组件 

## React component

有三种方式可以用来构建 React 








使用JSX写的代码就是使用React.createElement()进行转译


```
```
render(<Application />, document.querySelector('app'))

render(<Application />, document.querySelector('app'))
```




ReactDOM.render(
  element,
  document.getElementById('root')
);
```

this.props.children 为 React element 或 React element 数组
