
React数据类型分为 React element 和 React class，React element 是构建 React 组件 

## React element

使用JSX写的代码就是使用React.createElement()进行转译

```
var app = <div>123</div>
React.createElement("<div>123</div>")

{
	_context: Object,
	_owner: null,
	key: null,
	props: object,
	ref: null,
	type: "div"
}
```
```
render(<Application />, document.querySelector('app'))

render(<Application />, document.querySelector('app'))
```


```
React.createElement(
  type,
  [props],
  [...children]
)

React.cloneElement(
  element,
  [props],
  [...children]
)

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

this.props.children 为 React element 或 React element 数组
