# css那些事儿

----
> 本文重在讲述一些能提高前端工作效率的css技巧。


## 前言

css是前端开发工作中必不可少的一部分，同时也是最容易被忽视的一部分。尤其是现在，各种优秀的前端框架面世，导致许多前端开发人员蜂拥学习前端框架，却忽视了css这块。包括笔者自己也是，认为css够用即可，必要时直接百度就行。其实这种心态是不正确的，因为有时候百度出来的css，其实不是最优的，或者不利于后期维护。所以我们应该正视css。本文主要讲解在实际开发过程中，能提高工作效率的几个css技巧、实例以及一些注意点。

## 选择器

合理的利用伪元素选择器，可以使我们的代码更加简洁，语义化更佳明显。

### :empty

> 不支持ie8。

empty，就是空的意思。顾名思义，我们可以使用`:empty`来选择空元素。空元素就是没有标签内容的元素，如：`<div class='name'></div>`。

例如，我们目前拥有如下元素：

```
<li class='name'>li</li>
<li class='name'></li>
```

我们可以使用`.name:empty`来设置第二个li标签的样式，具体代码如下：

```
.name:empty{
	width: 100px;
	height: 200px;
	background-color: red;
	...
}
```

### :not

> 不支持ie8。

可以理解为非，如对非空元素的选择：`:not(:empty)`。

上述代码中，对于非空元素的样式设置如下：

```
.name:not(:empty){
	width: 10px;
	height: 10px;
	background-color: green;
	...
}
```

### :nth-of-type、:first-of-type、:last-of-type、:only-of-type

上述选择器分别为某种类型的第n个、第一个、最后一个、唯一的。此类选择器前面可以跟标签选择器、类选择器、id选择器。

其中`:nth-of-type`还可以携带参数，如：

```
:nth-of-type(even) // 表示某种类型的偶数个
:nth-of-type(3) // 表示某种类型的第三个
```


> 使用demo
> 
> div:first-of-type // 选中第一个div
> 
> img:last-of-type // 选中最后一个img标签

### [attr ** val]方括号选择器

首先声明，方括号选择器是笔者本人为了方便记忆所起的名字。该选择器如上表示，中间的`**`部分可以是`=、|=、^=、*=、$=、~=`。他们依次表示为：

* `=` 是attr严格等于val，完全匹配。
* `|=` 是attr的值以val开头，val必须为一整个单词或者后跟－连字符。
* `^=` 是attr的值以val开头，与`|=`的区别是val可以是一部分字符串。
* `*=` 是attr的值包含val。
* `$=` 是attr的值以val结束。
* `~=` 是attr的值包含val，此处val为一个单独的单词。

> 如：`div[class^=h]`表示匹配class属性值以h开头，并且是div元素。稍微有点绕口，请大家仔细体会。

### :first-child、:last-child、:nth-child、nth-last-child

上述选择器依次表示：

* 父元素的第一个子元素。
* 父元素的最后一个子元素。
* 第n个子元素。
* 顺序从后往前，第n个子元素。

> 如设置p标签的字体大小为20px，并且p标签作为第一个子元素。
> 
```
p:first-child{
	font-size: 20px;
}
```

### :only-child

匹配唯一子元素。

> 如设置p标签的字体大小为20px，并且p标签作为唯一子元素。
> 
```html
<div><p>111</p></div>
<div><p>111</p><div></div></div>
```
> 使用`p:only－child`选择器会匹配到第一个div标签下的p标签，而不会匹配第二个div下的。

### :root

设置html标签。

> 如设置html的字体大小为20px。
> 
```
:root{
	font-size: 20px;
}
```

### selector1+selector2

表示选择紧跟在selector1之后的selector2元素。此处是紧跟，不是内部。

> 如h1+h2表示选择紧跟在h1标签之后的h2标签。

## 注意点

### padding和width一起使用

有的浏览器下，我们如果同时设置padding和width之后，实际的渲染效果可能会超出我们的预期。如：设置width为100px，设置padding为10px。那么最终可能会发现实际的width为120px（含左右padding）。解决方法就是设置box-sizing属性为border-box。（请确保使用合适的浏览器厂商前缀或者使用工具）

### ios中真正1像素边框

在移动端开发时，经常会做1px的边框。由于iphone中retina屏的原因，1px会占用2个或者3个像素点。这样就导致你在写css样式的时候是1px，但是实际显示就会比较粗。

关于这个问题，网上已经有很多解决方法，各位读者可以自行百度ios 1px 边框的实现，此处不做过多的赘述。

### rem在安卓中的问题

笔者在最近项目的开发过程中，发现了定义1px的边框，使用工具转化为rem（为了适配不同机型），可能会在安卓4.4版本的webview中不显示。这个问题的原因是项目中使用了px转rem的工具（postcss－px2rem）。针对此问题的解决方法就是在定义边框的时候不使用rem转换。

### 绝对定位尽量少用

在开发样式的过程中，可能会觉得使用绝对定位非常方便，可以随意修改子元素位置，非常灵活。但是笔者在这里还是建议在开发过程中尽量少使用绝对定位。因为它在使用的过程中，稍有不慎就会发现子元素没有出现在我们预期的位置（可能它所相对的父元素没有指明导致）。其次是绝对定位有可能将元素放在半个像素上，导致元素看起来模糊。

## 命名规范

我们在开发比较大的项目的时候，最好采用一套比较好的命名规范。虽然语义化的class命名能够清晰的了解每个class的作用，但是却看不出class之间的关系。如果是采用一定命名规范的css代码，它将易维护、语义更清晰，后期修改的时候不必再打开调试工具查找相关样式，这样能极大程度提升效率。

此处为着重介绍大名鼎鼎的BEM命名规范。

简单来讲就是（block、element、modifier），三者之间只能使用__和--来连接。我们可以将这个命名规范这样来理解：

```
.block{} // 代表某一部分、某个父元素
.block__element{} // 父元素下的子元素
.block--modifier{} // 元素的其它类名，如div元素有block类，也有a类，那么在书写的时候可以写成block以及block--a（两个类名）
```

> 此处的block、element、modifier可以根据实际需要来定义。

此命名规范同样适用于scss。在scss中可以使用`@at-root`来实现这一命名规范，从而更好的管理以及维护样式代码。

## 常见实践

我们在开发过程中，会发现一些常用的样式布局。我们可以将这些样式布局纪录下来，方便查阅。此处我着重说一下水平垂直居中以及三列布局。

### 水平居中 

一、定宽的情况下，可以使用`margin: 0 auto`来实现。

二、定宽情况下，使用`position: absolute; left: 50%; margin-left: -(宽度一半)`来实现。

三、不定宽的情况下，同样适用absolute绝对定位，只不过是用`transform: translate(-50%,0);`来实现相对自身宽度的50%来位移。

四、使用flex布局，设置`justify-content:center`，主轴横向的情况

### 垂直居中

垂直居中和水平居中类似，我们可以改动水平居中中二、三的方法，来实现垂直居中。在水平居中和垂直居中是，我们在设置left或者top的时候，可以使用calc函数。如：`calc(50%, 负元素宽度或者高度的一半)` 。

同时，我们还可以使用flex布局，设置`align-items:center`，主轴横向的情况。

### 三栏布局

三栏布局，即为两边固定，中间自适应。该种布局经常出现在pc端，非常常见。如jd、淘宝首页等（左右导航栏定宽，中间内容区域自适应）。

一、绝对定位实现。

二、flex布局实现。如设置主轴为横向，中间部分`flex: 1`，左右部分`flex-basis: ???px`来实现。

当然，实现这种常见的布局，网上有很多解决方法，更有甚者居然总结出16种方法，实在丧心病狂。读者可以按照自己习惯以及需求自行选择。此处笔者只推荐绝对定位法和flex。

## 结语

以上是一些css的技巧和知识，虽说不是很复杂，但是我们很容易忽视这块，甚至是遗忘。所以关于css的使用技巧，我们应该重视起来，不断积累，从而提高开发效率。

同时css这块，其实还存在其它技巧以及知识，欢迎各位读者一起交流。

