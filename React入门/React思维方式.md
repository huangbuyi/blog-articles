在使用JavaScript开发大型、快速的网页应用时，React是我们的首选。在Facebook和Instagram，React很好地减少了我们的工作量。
React最强大部分之一，是让你在开发应用的同时，按照开发应用的思路去思考。这篇文章，将指导你通过几个步骤，用React开发一个可搜索数据表

## 从一个模型开始

假设我们已经有了一个JSON API和一个设计师设计的模型。看起来像这样:

![thinking-in-react-mock](https://facebook.github.io/react/img/blog/thinking-in-react-mock.png)

我们JSON API返回的数据看起来像这样：

```
[
  {category: "Sporting Goods", price: "$49.99", stocked: true, name: "Football"},
  {category: "Sporting Goods", price: "$9.99", stocked: true, name: "Baseball"},
  {category: "Sporting Goods", price: "$29.99", stocked: false, name: "Basketball"},
  {category: "Electronics", price: "$99.99", stocked: true, name: "iPod Touch"},
  {category: "Electronics", price: "$399.99", stocked: false, name: "iPhone 5"},
  {category: "Electronics", price: "$199.99", stocked: true, name: "Nexus 7"}
];
```

## 第一步：将UI分解为组件结构

首先，在模型图上框处每个组件（和子组件），并给组件命名。如果与设计师一起工作，他可能已经心中有数，不妨和他聊聊。他们Photoshop图层名可能就能成为你的组件名！

但如何划分父组件子组件呢？就像创建一个函数或对象一样。其中之一是[单一职责原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)，理想情况下，一个组件仅做一件事。如果一个组件太大，它应该被分解为更小的子组件。

如果经常把JSON数据模型展现给用户，你会发现，若模型创建正确，UI（以及组件结构）能很好的反映数据。这是因为UI和数据模型一定程度都是依附于信息架构的，这意味着，将UI分解为组件并不是最重要的，最重要的是，被分解的组件能准确表现你数据模型的一部分。

![thinking-in-react-components](https://facebook.github.io/react/img/blog/thinking-in-react-components.png)

可以看出，我们的app有五个组件，我们用斜体表示每个组件所代表的数据。

1. `FilterableProducTable`（可过滤产品表，橙色）：包含整个示例
2. `SearchBar`（搜索栏，蓝色）：接收*用户输入*
3. `ProductTable`（产品表，绿色）：根据*用户输入*，展示和过滤*数据集*
4. `ProductCategoryRow`（产品分类行，蓝绿色）：显示为每个*分类*的头部
5. `ProductRow`(产品行，红色)：每个*产品*显示为一行

观察`ProductTable`，你会发现表头（包含“Name”和“Price”标签部分）未被划分为一个组件。表头是否做为组件属于个人偏好，至于使用哪种方式，以下可以作为参考。本例中，表头作为`ProductTable`的一部分，因为表头属于渲染的数据集的一部分，所以表头在`ProductTable`的职责范围内。但如果表头变得更为复杂（如为添加排序功能），将表头分离为`ProductTableHeader`更合理。

现在，我们已经将模型划分为了组件，接着要安排组件的层次结构。这并不难，模型中一个组件出现在另一个组件的内部，在层次结构呈现为它的一个子级。

- FilterableProductTable
	- SearchBar
	- ProductTable
		- ProductCategeoryRow
		- ProductRow


## 第二步：创建React静态版本
```
class ProductCategoryRow extends React.Component {
  render() {
    return <tr><th colSpan="2">{this.props.category}</th></tr>;
  }
}

class ProductRow extends React.Component {
  render() {
    var name = this.props.product.stocked ?
      this.props.product.name :
      <span style={{color: 'red'}}>
        {this.props.product.name}
      </span>;
    return (
      <tr>
        <td>{name}</td>
        <td>{this.props.product.price}</td>
      </tr>
    );
  }
}

class ProductTable extends React.Component {
  render() {
    var rows = [];
    var lastCategory = null;
    this.props.products.forEach(function(product) {
      if (product.category !== lastCategory) {
        rows.push(<ProductCategoryRow category={product.category} key={product.category} />);
      }
      rows.push(<ProductRow product={product} key={product.name} />);
      lastCategory = product.category;
    });
    return (
      <table>
        <thead>
          <tr>
            <th>Name</th>
            <th>Price</th>
          </tr>
        </thead>
        <tbody>{rows}</tbody>
      </table>
    );
  }
}

class SearchBar extends React.Component {
  render() {
    return (
      <form>
        <input type="text" placeholder="Search..." />
        <p>
          <input type="checkbox" />
          {' '}
          Only show products in stock
        </p>
      </form>
    );
  }
}

class FilterableProductTable extends React.Component {
  render() {
    return (
      <div>
        <SearchBar />
        <ProductTable products={this.props.products} />
      </div>
    );
  }
}


var PRODUCTS = [
  {category: 'Sporting Goods', price: '$49.99', stocked: true, name: 'Football'},
  {category: 'Sporting Goods', price: '$9.99', stocked: true, name: 'Baseball'},
  {category: 'Sporting Goods', price: '$29.99', stocked: false, name: 'Basketball'},
  {category: 'Electronics', price: '$99.99', stocked: true, name: 'iPod Touch'},
  {category: 'Electronics', price: '$399.99', stocked: false, name: 'iPhone 5'},
  {category: 'Electronics', price: '$199.99', stocked: true, name: 'Nexus 7'}
];
 
ReactDOM.render(
  <FilterableProductTable products={PRODUCTS} />,
  document.getElementById('container')
);
```

现在，你已经有了组件的层次结构，是时候实现这个app了。最简单的方式，利用数据模型渲染一个无交互的UI版本。创建组件静态版本需要敲很多键盘，无需过多思考，而添加交互效果需要很多思考，不需要敲很多键盘，这正是极好的解耦过程。

用数据模型创建app的静态版本，你在创建组件时会想着复用其它组件，想着用*属性*来传递数据，而*属性*是父级向子级传递数据的方式。即便你熟悉*状态*的概念，创建静态版本时也**不要用任何状态**。状态仅用作交互，状态数据随时间而变化，因为静态版本无需任何交互，所以没必要使用任何状态。

在层级结构中，既可以自上而下开始创建组件（从`FilterableProductTable`开始），也可以自下而上开始创建组件（从`ProductRow`开始）。简单的例子中，自上而下的方式往往更简单，对于大型项目来说，自下而上的方式更简单，更容易测试。

该步完成时，你已经有了一些可复用的组件，这些组件渲染了你的数据模型。静态版本的组件仅有`render()`方法。顶层组件（`FilterableProductTable`）会把数据模型作为一个属性。如果数据模型发生变化，再次调用`ReactDOM.render()`，UI会被更新。你可以很容易的看出UI是如何被更新的，哪儿发生了改变。React的**单向数据流**（也叫单向数据绑定），使得一切变得模块化，变得快速。

### 简述：属性与状态

属性和状态是React中的两种数据模型，理解两者的区别非常重要。如果你无法确定两者的区别，请浏览[官方文档](https://facebook.github.io/react/docs/state-and-lifecycle.html)

## 第三步：确定描述UI的最少状态

为了让UI可以交互，需要能够触发数据模型发生改变。React的状态能让此变得简单。

为了能正确的创建app，你要考虑app需要的最少状态。关键在于**不重复**，使用最少的状态，计算出所有你需要的数据。例如，你创建一个TODO列表，只需要保留一个TODO项数组，不需要再使用一个变量来保存TODO项数量，当你需要渲染TODO项数目时，使用TODO项数组长度即可。

考虑示例应用中所有的数据，我们有：

 - 原始产品列表
 - 用户输入的搜索文本
 - 多选框的值 
 - 过滤过的产品列表

简单地问三个问题，让我们过一遍，看看哪些是状态：

 1. 是否为从父组件传入的属性？如果是，或许不是状态
 2. 是否是随时间不变的？如果是，或许不是状态
 3. 是否能通过组件中的其他状态或属性计算出来？如果是，不是状态

原始产品列表是从属性传入的，因此不是状态。搜索文本和多选框因为会发生变化，且不能通过计算得出，所以是状态。最后，过滤过的产品列表，可以通过原始产品列表、搜索文本和多选框值计算出来，因此它不是状态。

最终，我们的状态有：

 - 用户输入的搜索文本
 - 多选框的值


## 第四步：确定状态应该放置的位置
```
class ProductCategoryRow extends React.Component {
  render() {
    return (<tr><th colSpan="2">{this.props.category}</th></tr>);
  }
}

class ProductRow extends React.Component {
  render() {
    var name = this.props.product.stocked ?
      this.props.product.name :
      <span style={{color: 'red'}}>
        {this.props.product.name}
      </span>;
    return (
      <tr>
        <td>{name}</td>
        <td>{this.props.product.price}</td>
      </tr>
    );
  }
}

class ProductTable extends React.Component {
  render() {
    var rows = [];
    var lastCategory = null;
    this.props.products.forEach((product) => {
      if (product.name.indexOf(this.props.filterText) === -1 || (!product.stocked && this.props.inStockOnly)) {
        return;
      }
      if (product.category !== lastCategory) {
        rows.push(<ProductCategoryRow category={product.category} key={product.category} />);
      }
      rows.push(<ProductRow product={product} key={product.name} />);
      lastCategory = product.category;
    });
    return (
      <table>
        <thead>
          <tr>
            <th>Name</th>
            <th>Price</th>
          </tr>
        </thead>
        <tbody>{rows}</tbody>
      </table>
    );
  }
}

class SearchBar extends React.Component {
  render() {
    return (
      <form>
        <input type="text" placeholder="Search..." value={this.props.filterText} />
        <p>
          <input type="checkbox" checked={this.props.inStockOnly} />
          {' '}
          Only show products in stock
        </p>
      </form>
    );
  }
}

class FilterableProductTable extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      filterText: '',
      inStockOnly: false
    };
  }

  render() {
    return (
      <div>
        <SearchBar
          filterText={this.state.filterText}
          inStockOnly={this.state.inStockOnly}
        />
        <ProductTable
          products={this.props.products}
          filterText={this.state.filterText}
          inStockOnly={this.state.inStockOnly}
        />
      </div>
    );
  }
}


var PRODUCTS = [
  {category: 'Sporting Goods', price: '$49.99', stocked: true, name: 'Football'},
  {category: 'Sporting Goods', price: '$9.99', stocked: true, name: 'Baseball'},
  {category: 'Sporting Goods', price: '$29.99', stocked: false, name: 'Basketball'},
  {category: 'Electronics', price: '$99.99', stocked: true, name: 'iPod Touch'},
  {category: 'Electronics', price: '$399.99', stocked: false, name: 'iPhone 5'},
  {category: 'Electronics', price: '$199.99', stocked: true, name: 'Nexus 7'}
];

ReactDOM.render(
  <FilterableProductTable products={PRODUCTS} />,
  document.getElementById('container')
);

```

OK，我们已经确定了app的最少状态。接下来，我们要确定哪个组件是可变的，或者说状态属于哪个组件。

记住，React在层级及结构中，数据向下流动。这或许不能马上清楚状态究竟属于哪个组件。**这经常也是初学者认识React最有挑战性的部分**。所以，按照以下步骤，弄清这个问题：

对于应用中的每个状态：
	
- 确认每个需要这个状态进行渲染的组件
- 找到它们共有的父组件（层级结构中，所有需要这个状态的组件之上的组件）
- 让共有父组件，或者比共有父组件更高层级的组件持有该状态
- 如果你找不到合适的组件持有该状态，创建一个新的组件，简单持有该状态。把这个组件插入层级结构共有父组件之上

将这个策略用到我们的应用中：

- `ProductTable`要根据状态过滤产品列表，`SearchBar`要显示搜索文本和多选框状态
- 共有父组件是`FilterableProductTable`
- `FilterableProductTable`持有过滤文本和多选框值很合理

Cool，就这样决定把状态放置到`FilterableProductTable`。首先，在`FilterableProductTable`的`constructor`中添加示例属性`this.state = {filterText: '', inStockOnly: false} `，来初始化状态。然后，将`filterText`和`isStockOnly`作为属性传入`ProductTable`和`SearchBar`。最后，使用这些属性过滤`ProductTable`中的行，和设置`SearchBar`中表单的值

## 第五步：添加反向数据流
```
class ProductCategoryRow extends React.Component {
  render() {
    return (<tr><th colSpan="2">{this.props.category}</th></tr>);
  }
}

class ProductRow extends React.Component {
  render() {
    var name = this.props.product.stocked ?
      this.props.product.name :
      <span style={{color: 'red'}}>
        {this.props.product.name}
      </span>;
    return (
      <tr>
        <td>{name}</td>
        <td>{this.props.product.price}</td>
      </tr>
    );
  }
}

class ProductTable extends React.Component {
  render() {
    var rows = [];
    var lastCategory = null;
    this.props.products.forEach((product) => {
      if (product.name.indexOf(this.props.filterText) === -1 || (!product.stocked && this.props.inStockOnly)) {
        return;
      }
      if (product.category !== lastCategory) {
        rows.push(<ProductCategoryRow category={product.category} key={product.category} />);
      }
      rows.push(<ProductRow product={product} key={product.name} />);
      lastCategory = product.category;
    });
    return (
      <table>
        <thead>
          <tr>
            <th>Name</th>
            <th>Price</th>
          </tr>
        </thead>
        <tbody>{rows}</tbody>
      </table>
    );
  }
}

class SearchBar extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
  }
  
  handleChange() {
    this.props.onUserInput(
      this.filterTextInput.value,
      this.inStockOnlyInput.checked
    );
  }
  
  render() {
    return (
      <form>
        <input
          type="text"
          placeholder="Search..."
          value={this.props.filterText}
          ref={(input) => this.filterTextInput = input}
          onChange={this.handleChange}
        />
        <p>
          <input
            type="checkbox"
            checked={this.props.inStockOnly}
            ref={(input) => this.inStockOnlyInput = input}
            onChange={this.handleChange}
          />
          {' '}
          Only show products in stock
        </p>
      </form>
    );
  }
}

class FilterableProductTable extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      filterText: '',
      inStockOnly: false
    };
    
    this.handleUserInput = this.handleUserInput.bind(this);
  }

  handleUserInput(filterText, inStockOnly) {
    this.setState({
      filterText: filterText,
      inStockOnly: inStockOnly
    });
  }

  render() {
    return (
      <div>
        <SearchBar
          filterText={this.state.filterText}
          inStockOnly={this.state.inStockOnly}
          onUserInput={this.handleUserInput}
        />
        <ProductTable
          products={this.props.products}
          filterText={this.state.filterText}
          inStockOnly={this.state.inStockOnly}
        />
      </div>
    );
  }
}


var PRODUCTS = [
  {category: 'Sporting Goods', price: '$49.99', stocked: true, name: 'Football'},
  {category: 'Sporting Goods', price: '$9.99', stocked: true, name: 'Baseball'},
  {category: 'Sporting Goods', price: '$29.99', stocked: false, name: 'Basketball'},
  {category: 'Electronics', price: '$99.99', stocked: true, name: 'iPod Touch'},
  {category: 'Electronics', price: '$399.99', stocked: false, name: 'iPhone 5'},
  {category: 'Electronics', price: '$199.99', stocked: true, name: 'Nexus 7'}
];

ReactDOM.render(
  <FilterableProductTable products={PRODUCTS} />,
  document.getElementById('container')
);
```

目前为止，我们已经用属性和状态，创建了具有在层级结构中至上而下的数据流。现在，是时候支持反向的数据流动，让底层表单组件能够更新`FilterableProductTable`中的状态。

React的单向数据流动使得程序更加清晰易懂，但相比双向数据绑定，确实需要多打些代码。

如果你尝试在当前示例中，输入文本或选择多选框，会发现React忽略了你的输入。这是故意的，因为我们要设置`input`的`value`属性要恒等于`FilterableProductTable`传入的状态。

思考下我们期望的状况。我们希望，表单中无论何时发生改变，用户的输入要能够更新状态。因为组件应该只更新自己的状态，所以`FilterableProductTable`会将回调函数传递给`SearchBar`，`SearchBar`会在要更新状态时调用回调函数。我们用`onChange`事件通知状态更新。从`FilterableProductTable`传入的回调函数会调用`setState()`，从而更新组件。

虽然听起来有点复杂，但是也就添加几行代码。它非常清晰地表现出了app中的数据流动。

## 就这样

希望此文能带给你使用React创建组件和应用的思路。尽管你可能需要打比平时更多的代码，但记住，代码看着总是远比打着的时候多，并且这些代码都是模块化、清晰而易读的。当你开始开发大型组件库的时候，你将会庆幸React的数据流清晰和模块化的特性，并且随着代码的复用，代码规模会开始缩小。

## 原文链接

 - [thinking-in-react](https://facebook.github.io/react/docs/thinking-in-react.html)