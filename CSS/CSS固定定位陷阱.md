在日常开发中，经常使用固定定位`position:fixed`，使得元素相对于屏幕视口定位，常常用在头部、侧边抽屉菜单、FAB等。固定定位的元素，不会随窗口滚动而移动，与父元素的位置无关，但真的是这样吗？

假设页面上有一些固定定位元素，如`<header>`和`<menu>`，让我们在body的样式中添加一句代码`transform: translate(30px,30px)`：

```
// html
<header></header>
<menu></menu>

// css
body {transform: translate(30px,30px)}
header {
  position: fixed;
  left: 0;
  top: 0;
  right: 0;
  height: 68px;
  background: #2196F3;
}
menu {
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  width: 240px;
  background: #F39621;
}
```
[固定定位失效](http://codepen.io/huangbuyi/pen/BpgmZZ)

发现了吗，固定定位的元素发生了偏移，它们不再是相对于屏幕视口进行定位，而且menu元素直接从视图上消失了。再来看一个例子：

```
// html
<div class='container'>
  <div class='fix'>/div>
</div>

// css
.container{
  width: 200px;
  height: 200px;
  background: #6699FF;
  animation: move 4s cubic-bezier(0.4,0,0.6,1) infinite;
}
.fix{
  position: fixed;
  top: 20px;
  left: 20px;
  width: 200px;
  height: 200px;
  background: #9966FF;
  color:#FFF;
}
@keyframes move{
  0% {transform:translateX(100px);}
  50% {transform:translateX(500px);}
  100% {transform:translateX(100px);}
}
```
[固定定位不固定](http://codepen.io/huangbuyi/pen/mRYXbg)

固定定位的元素竟然动了起来，究竟发生了什么？

## 固定定位定义

> fixed: Do not leave space for the element. Instead, position it at a specified position relative to the screen's viewport and don't move it when scrolled. When printing, position it at that fixed position on every page. This value always creates a new stacking context. When an ancestor has the transform property set to something different from none then this ancestor is used as container instead of the viewport  

简而言之，一般情况下，固定定位是相对于视口定位的。但当它的祖级元素的`transform`属性有非`none`值时，固定定位不再相对于视口定位，而是相对于该祖级元素定位。这也正是固定定位不符合预期的原因。

结合上面的例子：body元素添加`transform: translate(30px,30px)`后，`<header>`和`<menu>`元素的变成了相对于body进行定位，因此，这两倆都发生偏移。而body的高度为0，`<menu>`因为是上下定位，所以它的高度也变为了0，所以`<menu>`直接'消失'了。

在第二个例子中，`container`元素添加了`transform`动画，使得内部的所有元素也随它一起运动。

## 远离陷阱

日常开发，尤其是在组件开发中，尽量将固定定位的元素挂载到`document.body`下，我们很少会为body添加变换样式。在开发具有弹框的组件，如Modal、Dialog等组件时，你无法确定其祖级元素是否会有变换样式，因此，弹框元素挂载在`document.body`下才是最万无一失的选择。
